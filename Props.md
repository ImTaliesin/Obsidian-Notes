[[React JS]] [[Typescript]] [[Type]]

```
import {type PropsWithChildren } from 'react';

type CourseGoalProps = PropsWithChildren<{ title: string }>

export default function CourseGoal({title,children}: CourseGoalProps) {
    return (
        <article className='goal-item'>
            <div>
                <h2>{title}</h2>
                <p>{children}</p>
            </div>
            <button>Delete</button>
        </article>
    )
}
```
```
import type { ReactNode } from 'react';

type headerProps = {
    image: {
        src: string;
        alt: string;
    }
    children: ReactNode
}

export default function Header({ image, children }: headerProps) {
    return (
        <header>
            <img {...image} />
            {children}
        </header>
    )
}
```

App:
```
import CourseGoal from './components/CourseGoal'
import Header from './components/Header.tsx'
import goalsImg from './assets/goals.jpg'


export default function App() {
  return (
    <main>
      <Header image={{ src: goalsImg, alt: 'A list of goals' }} />
        <h1>Your Course Goals</h1>
      <CourseGoal title='Learn React'>
        <p>Learn it please</p>
      </CourseGoal>
    </main>
  )
}

```