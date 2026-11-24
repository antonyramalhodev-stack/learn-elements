# Learn elements

Interactive lesson elements for React sites, with GitHub's look: [Primer](https://primer.style), Octicons, and Mona Sans.

This repository holds only what a site needs to use the elements. To see them run, open the [demo site](https://antonyramalhodev-stack.github.io/learn-interactive-demo/).

> [!NOTE]
> This is a prototype. It is not an official GitHub project.

## What's inside

| Path | What it is |
| --- | --- |
| `elements/` | The elements. Each one is one `.tsx` file and one `.module.css` file. |
| `elements/setup.css` | The one-time setup: the Primer design tokens and the Mona Sans font. |

## What your site needs

- React 19.
- A build tool that handles TypeScript and CSS Modules. Next.js and Vite both do.
- MDX, if you write lessons in Markdown. The elements also work from plain `.tsx` files.

## Set up

1. Copy the `elements` folder into your site, for example to `src/elements`.
2. Install the packages:

   ```bash
   npm install @primer/react @primer/primitives @primer/octicons-react @fontsource-variable/mona-sans mermaid
   ```

3. Import the setup file once, in the file that loads your site's global CSS:

   ```ts
   import './elements/setup.css';
   ```

4. Set the color mode on the `html` element. Use `light` or `dark`:

   ```html
   <html data-color-mode="light" data-light-theme="light" data-dark-theme="dark">
   ```

   If your site has a theme switch, change `data-color-mode` and the elements follow.

5. Give the elements to your MDX pages:

   ```tsx
   import { elements } from './elements';
   import Lesson from './lesson.mdx';

   <Lesson components={elements} />
   ```

   If your site already has a list of MDX components, add them there: `{ ...yourComponents, ...elements }`.

## The elements

**Explain.** Each one plays when the reader scrolls to it. When the reader's system asks for reduced motion, it shows its end state.

| Tag | What it does |
| --- | --- |
| `Terminal` | A terminal session that types itself |
| `GitGraph` | A commit graph that draws itself one step at a time |
| `Timeline` with `Event` | A sequence of events that appear one after another |
| `CodeTour` with `Stop` | A highlight that walks through a code sample |
| `WorkflowRun` | Jobs and steps that run in order |
| `Mermaid` | A diagram from text that uses the Primer colors |

**Practice.**

| Tag | What it does |
| --- | --- |
| `Quiz` with `Answer` | A single-choice question with feedback for each answer |
| `CommandExercise` | The learner types a command and the page checks it |
| `Flashcards` with `Flashcard` | A deck of terms that flip to show the answer |
| `Checklist` with `Task` | A checklist with a progress bar that the browser saves |
| `LessonMeta` | Chips for time, level, and prerequisites |

## Write a lesson

A list of items uses child tags:

```mdx
<Quiz question="Which command creates a branch and switches to it?">
  <Answer correct feedback="The -c option creates the branch.">git switch -c docs</Answer>
  <Answer feedback="The -d option deletes a branch.">git branch -d docs</Answer>
</Quiz>

<Flashcards>
  <Flashcard term="Fork">Your own copy of someone else's repository.</Flashcard>
  <Flashcard term="Clone">A copy of a repository on your computer.</Flashcard>
</Flashcards>

<Checklist id="first-lesson" title="Lesson tasks">
  <Task>Create a branch</Task>
  <Task>Open a pull request</Task>
</Checklist>

<Timeline>
  <Event icon="commit" title="You commit a change on a branch" />
  <Event icon="pull-request" color="success" title="You open a pull request" />
  <Event icon="merge" color="done" title="A maintainer merges the pull request" />
</Timeline>
```

A script uses plain text between the tags, inside `` {`...`} ``:

```mdx
<Terminal title="Create a branch">{`
$ git switch -c docs
Switched to a new branch 'docs'
# Push the branch and set its upstream
$ git push -u origin docs
`}</Terminal>

<GitGraph title="Feature branch flow">{`
commit main: Start from the default branch
branch feature from main: Create a branch named feature
commit feature: Commit your first change
merge feature into main: Merge feature into main
`}</GitGraph>

<WorkflowRun title="Deploy" trigger="git push origin main" result="The site is live">{`
build: Install dependencies, Build the static site
deploy: Deploy to GitHub Pages
`}</WorkflowRun>

<CommandExercise prompt="Create a branch named fix-typo and switch to it." hint="Use git switch with the -c option.">{`
git switch -c fix-typo
git checkout -b fix-typo
`}</CommandExercise>

<Mermaid label="Flow from a branch to a pull request">{`
flowchart LR
  A[Create a branch] --> B[Open a pull request]
`}</Mermaid>
```

What each script line means:

| Element | Lines |
| --- | --- |
| `Terminal` | `$` starts a command and `#` starts a comment. Every other line is output. |
| `GitGraph` | `commit BRANCH: caption`, `branch NEW from BRANCH: caption`, or `merge BRANCH into TARGET: caption` |
| `WorkflowRun` | One job on each line: `job: step, step` |
| `CommandExercise` | One accepted command on each line |
| `Mermaid` | The Mermaid chart text |

`CodeTour` takes the code in a `code` property and one `Stop` for each highlight:

```mdx
<CodeTour
  title="deploy.yml"
  code={`on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest`}
>
  <Stop lines="1-3" title="The trigger">The workflow starts on every push to the main branch.</Stop>
  <Stop lines="5-7" title="The build job">The job runs on a GitHub-hosted runner.</Stop>
</CodeTour>
```

Each element has a page on the [demo site](https://antonyramalhodev-stack.github.io/learn-interactive-demo/docs) that shows it running and lists its properties.

The elements are React components, so a `.tsx` file can use them too:

```tsx
import { Answer, Quiz } from './elements';

<Quiz question="Does the element work without MDX?">
  <Answer correct feedback="It is a React component.">Yes</Answer>
  <Answer feedback="Try the other answer.">No</Answer>
</Quiz>
```

## Add an element

1. Create `elements/my-element.tsx`. Wrap the content in `Panel` so it matches the others.
2. Create `elements/my-element.module.css` if the element needs its own styles. Use Primer tokens for every color and size.
3. Add the element to `elements` in `elements/index.ts`.

```tsx
'use client';

import { StarIcon } from '@primer/octicons-react';
import { Panel } from './panel';

export function MyElement({ children }: { children: React.ReactNode }) {
  return (
    <Panel title="My element" icon={<StarIcon size={16} aria-hidden />}>
      {children}
    </Panel>
  );
}
```

The shared parts are small:

| File | What it gives every element |
| --- | --- |
| `panel.tsx` | The frame: a title bar and a body |
| `hooks.ts` | In-view detection, reduced motion, auto stepping, copy, and saved state |
| `step-nav.tsx` | Previous, play, and next buttons |
| `script.ts` | The reader for text between the tags |

## Where the elements run

- **React sites.** Every element runs as it is. Nothing needs a server, and saved state stays in the reader's browser.
- **Sites without React.** The folder doesn't run there as it is.
- **Microsoft Learn modules.** The elements don't run there, because Learn doesn't run custom components.

## Tested with

| Package | Version |
| --- | --- |
| `react` and `react-dom` | 19.3.1 |
| `@primer/react` | 38.47.0 |
| `@primer/primitives` | 11.10.0 |
| `@primer/octicons-react` | 19.42.0 |
| `@fontsource-variable/mona-sans` | 5.3.0 |
| `mermaid` | 12.3.0 |
| `typescript` | 7.0.2 |

Build tools: Next.js 16.4.6 (the demo site) and Vite 8.3.8 with `@mdx-js/rollup` 3.1.1 (a bare page with no other CSS).
