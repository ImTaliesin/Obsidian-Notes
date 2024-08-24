[[tailwind css]]

How to allow your classnames to be overwritten in tailwind

Main
~SRC
~ ~ APP
~ ~ ~ files.ts
~LIB
~ ~ utils.ts

in utils.ts 
```
import { type ClassValue, clsx } from "clsx"

import { twMerge } from "tailwind-merge"

  

export function cn(...inputs: ClassValue[]) {

  return twMerge(clsx(inputs))

}
```

In your components 
```
className={cn('flex h-lg')}
```