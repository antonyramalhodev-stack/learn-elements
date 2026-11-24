# Learn elements

A small library of interactive lesson elements for React sites, with GitHub's look: [Primer](https://primer.style), Octicons, and Mona Sans.

This repository holds only what a site needs to use the elements. To see every element run, open the [demo site](https://antonyramalhodev-stack.github.io/learn-interactive-demo/).

> [!NOTE]
> This is a prototype. It is not an official GitHub project.

## How the team uses this library

| You want to | Go to |
| --- | --- |
| Add the library to a site | [Set up a site](#set-up-a-site) |
| Write a lesson with the elements | [Use the elements in a lesson](#use-the-elements-in-a-lesson) |
| Look up the properties of an element | [Element reference](#element-reference) |
| Change an element or add a new one | [Change the library](#change-the-library) |

The setup happens once for each site. After that, writers only add tags to lesson pages.

## What's inside

| Path | What it is |
| --- | --- |
| `elements/` | The elements. Each one is one `.tsx` file and one `.module.css` file. |
| `elements/setup.css` | The one-time setup: the Primer design tokens and the Mona Sans font. |

## Set up a site

The site needs:

- React 19.
- A build tool that handles TypeScript and CSS Modules. Next.js and Vite both do.
- MDX, if you write lessons in Markdown. The elements also work from plain `.tsx` files.

Then follow these steps:

1. Copy the `elements` folder into the site, for example to `src/elements`.
2. Install the packages:

   ```bash
   npm install @primer/react @primer/primitives @primer/octicons-react @fontsource-variable/mona-sans mermaid
   ```

3. Import the setup file once, in the file that loads the site's global CSS:

   ```ts
   import './elements/setup.css';
   ```

4. Set the color mode on the `html` element. Use `light` or `dark`:

   ```html
   <html data-color-mode="light" data-light-theme="light" data-dark-theme="dark">
   ```

   If the site has a theme switch, change `data-color-mode` and the elements follow.

5. Give the elements to the MDX pages:

   ```tsx
   import { elements } from './elements';
   import Lesson from './lesson.mdx';

   <Lesson components={elements} />
   ```

   If the site already has a list of MDX components, add them there: `{ ...yourComponents, ...elements }`.

## Use the elements in a lesson

### Choose an element

**Explain a concept**

| To | Use |
| --- | --- |
| Show commands and their output | [`Terminal`](#terminal) |
| Show how branches, commits, and merges relate | [`GitGraph`](#gitgraph) |
| Show events in the order they happen | [`Timeline`](#timeline) |
| Explain a code sample one part at a time | [`CodeTour`](#codetour) |
| Show jobs and steps that run in order | [`WorkflowRun`](#workflowrun) |
| Draw a diagram from text | [`Mermaid`](#mermaid) |

**Let the learner practice**

| To | Use |
| --- | --- |
| Ask one question with feedback | [`Quiz`](#quiz) |
| Have the learner type a command | [`CommandExercise`](#commandexercise) |
| Review terms | [`Flashcards`](#flashcards) |
| Track the tasks of a lesson | [`Checklist`](#checklist) |
| Show the time, level, and prerequisites | [`LessonMeta`](#lessonmeta) |

### Two ways to write an element

A list of items uses child tags:

```mdx
<Flashcards>
  <Flashcard term="Fork">Your own copy of someone else's repository.</Flashcard>
  <Flashcard term="Clone">A copy of a repository on your computer.</Flashcard>
</Flashcards>
```

A script uses plain text between the tags, inside `` {`...`} ``:

```mdx
<Terminal>{`
$ git switch -c docs
Switched to a new branch 'docs'
`}</Terminal>
```

### What every element does

- An animation starts when the reader scrolls to the element.
- When the reader's system asks for reduced motion, the element shows its end state.
- Colors follow the light or dark mode of the page.
- Nothing needs a server. Saved state stays in the reader's browser.

## Element reference

Each element also has a page on the [demo site](https://antonyramalhodev-stack.github.io/learn-interactive-demo/docs) that shows it running.

### Terminal

A terminal session that types itself.

```mdx
<Terminal title="Create a branch">{`
$ git switch -c docs
Switched to a new branch 'docs'
# Push the branch and set its upstream
$ git push -u origin docs
`}</Terminal>
```

Start a command with `$` and a comment with `#`. Every other line is output.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `title` | string | `Terminal` | The text in the title bar |
| `loop` | boolean | `false` | Starts again a few seconds after the last line |
| `speed` | number | `32` | Milliseconds for each typed character |

### GitGraph

A commit graph that draws itself one step at a time.

```mdx
<GitGraph title="Feature branch flow">{`
commit main: Start from the default branch
branch feature from main: Create a branch named feature
commit feature: Commit your first change
merge feature into main: Merge feature into main
`}</GitGraph>
```

Write one event on each line. The text after the colon is the caption for that step. A line takes one of three forms:

| Line | What it draws |
| --- | --- |
| `commit BRANCH: caption` | A commit on a branch |
| `branch NEW from BRANCH: caption` | A new branch that starts from another branch |
| `merge BRANCH into TARGET: caption` | A merge of one branch into another |

A line in any other form causes an error.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `title` | string | `Commit graph` | The text in the title bar |
| `autoPlay` | boolean | `true` | Plays the steps when the graph scrolls into view |
| `loop` | boolean | `false` | Starts again after the last step |

### Timeline

A sequence of events that appear one after another.

```mdx
<Timeline>
  <Event icon="commit" title="You commit a change on a branch" />
  <Event icon="pull-request" color="success" title="You open a pull request" />
  <Event icon="review" color="accent" title="A reviewer reads the change">
    The reviewer can comment, approve, or request changes.
  </Event>
  <Event icon="merge" color="done" title="A maintainer merges the pull request" />
</Timeline>
```

Text between the `Event` tags shows as a detail under the title.

| Property of `Event` | Type | Default | What it does |
| --- | --- | --- | --- |
| `title` | string | Required | The event |
| `icon` | `dot`, `commit`, `branch`, `pull-request`, `merge`, `review`, `comment`, `issue`, `check`, `workflow`, `tag`, or `rocket` | `dot` | The Octicon in the badge |
| `color` | `accent`, `success`, `attention`, `severe`, `danger`, or `done` | None | The badge color, from Primer |

### CodeTour

A highlight that walks through a code sample.

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

Add one `Stop` for each highlight. The text between the `Stop` tags is the note for that highlight.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `code` | string | Required | The code sample |
| `title` | string | `Code tour` | The text in the title bar, for example a file name |
| `autoPlay` | boolean | `false` | Moves through the stops on a timer until the reader takes over |

| Property of `Stop` | Type | Default | What it does |
| --- | --- | --- | --- |
| `lines` | string | Required | One line, or a range such as `3-6`. Lines start at 1. |
| `title` | string | Required | The heading of the note |

### WorkflowRun

Jobs and steps that run in order.

```mdx
<WorkflowRun title="Deploy" trigger="git push origin main" result="The site is live">{`
build: Install dependencies, Build the static site
deploy: Deploy to GitHub Pages
`}</WorkflowRun>
```

Write one job on each line: `job: step, step`.

The element is an animation. It shows the order of events from the text you give it, and it doesn't read a real workflow run.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `trigger` | string | Required | The event that starts the run |
| `result` | string | Required | The line that appears when every job is done |
| `title` | string | `Workflow run` | The text in the title bar |

### Mermaid

A diagram from text that uses the Primer colors.

```mdx
<Mermaid label="Flow from a branch to a pull request">{`
flowchart LR
  A[Create a branch] --> B[Open a pull request]
`}</Mermaid>
```

Write the [Mermaid](https://mermaid.js.org) diagram text between the tags.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `label` | string | Required | A short description of the diagram for screen readers |

### Quiz

A single-choice question with feedback for each answer.

```mdx
<Quiz question="Which command creates a branch and switches to it?">
  <Answer correct feedback="The -c option creates the branch.">git switch -c docs</Answer>
  <Answer feedback="The -d option deletes a branch.">git branch -d docs</Answer>
</Quiz>
```

Add one `Answer` for each choice, and mark the right one with `correct`. The learner sees the feedback after they check their answer.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `question` | string | Required | The question the learner answers |

| Property of `Answer` | Type | Default | What it does |
| --- | --- | --- | --- |
| `correct` | boolean | `false` | Marks the right answer |
| `feedback` | string | None | Text the learner sees after they check this answer |

### CommandExercise

The learner types a command and the page checks it.

```mdx
<CommandExercise
  prompt="Create a branch named fix-typo and switch to it."
  hint="Use git switch with the -c option."
>{`
git switch -c fix-typo
git checkout -b fix-typo
`}</CommandExercise>
```

Write the accepted commands between the tags, one on each line. Extra spaces in the learner's answer don't matter.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `prompt` | string | Required | The task the learner completes |
| `hint` | string | None | Text behind a **Show hint** button |
| `placeholder` | string | `Type the command` | Placeholder text for the input |

### Flashcards

A deck of terms that flip to show the answer.

```mdx
<Flashcards>
  <Flashcard term="Repository">A project folder that Git tracks, with its full history.</Flashcard>
  <Flashcard term="Fork">Your own copy of someone else's repository.</Flashcard>
</Flashcards>
```

Add one `Flashcard` for each term. The text between the tags is the answer.

| Property of `Flashcard` | Type | Default | What it does |
| --- | --- | --- | --- |
| `term` | string | Required | The text on the front of the card |

### Checklist

A checklist with a progress bar that the browser saves.

```mdx
<Checklist id="first-pull-request" title="Lesson tasks">
  <Task>Create a branch</Task>
  <Task>Open a pull request</Task>
</Checklist>
```

Add one `Task` for each item. Give every checklist its own `id`, because the browser saves the progress under that key.

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `id` | string | Required | A unique key for the saved progress |
| `title` | string | `Track your progress` | The label above the list |

### LessonMeta

Chips for time, level, and prerequisites.

```mdx
<LessonMeta duration="10 minutes" level="Beginner" requires="Git and a GitHub repository" />
```

| Property | Type | Default | What it does |
| --- | --- | --- | --- |
| `duration` | string | Required | How long the lesson takes |
| `level` | string | Required | The level of the lesson |
| `requires` | string | None | What the learner needs before they start |

### Use an element from a .tsx file

The elements are React components, so a `.tsx` file can use them too:

```tsx
import { Answer, Quiz } from './elements';

<Quiz question="Does the element work without MDX?">
  <Answer correct feedback="It is a React component.">Yes</Answer>
  <Answer feedback="Try the other answer.">No</Answer>
</Quiz>
```

## Change the library

Each site keeps its own copy of the `elements` folder. After you change an element here, copy the folder to each site again.

To add an element:

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

- **React sites.** Every element runs as it is.
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
