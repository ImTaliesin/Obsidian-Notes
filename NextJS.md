[[Typescript]] [[MongoDB]]

When you first start up a project:
![[Pasted image 20240207123108.png]]

Make a table like this and fill it out
![[Pasted image 20240207123809.png]]

Implement Path Helpers![[Pasted image 20240207140952.png]]
Next implements caching in several locations and they can lead to unexpected behavior.

- Data Cache
	- Responses from requests made with 'fetch' are stored and used across requests.
- Router Cache
	- 'Soft' navigation between routes are cached in the browser and reused when a user revisits a page.
- Request Memoization
	- Make two ore more 'GET' requests with 'fetch' during a user's request to your server? only one 'GET' request is actually executed 
- Full Route Cache
	- **_At build time_**, Next decides if your route is _static_ or _dynamic_. If it is static, the page is rendered and the result is stored. In production, users are given this pre-rendered result. 

What makes a page dynamic?
- Calling a dynamic function or refencing a dynamic variable when your route renders.
	- cookies.set()/cookies.delete()
	- useSearchParams()/searchParams prop
- Assigning specific route segment config options
	- export dynamic = 'force-dynamic'
	- export const revalidate = 0
- Calling 'fetch and optng out of caching of the responsr
	- fetch( '...', {next: { revalidate: 0 }});

When/How to keep pages static
There are several ways to control caching

- Time-Based
	- Every X seconds, ignore the cached response and fetch new data
		 ```
		 export const revalidate = 3
		    export default async function Page() {
			    const snippets = await db.snippets.findMany();
			return <div>{snippets.map(..)}</div>
		    }
		 ```
- On-Demand
	- Forcibly purge a cached reponse
- Disable Caching

OAuth
How to see if a user is signed in via server component
```
import * as actions from '@actions'
import {auth} from '@/app/auth';
export default async function Home() {
	const session = await auth();
	return (
		<div>
			<form action={actions.signIn}>
				<Button type='submit'>Sign in</Button>
			</form>
			<form action={actions.signOut}>
				<Button type='submit'>Sign out</Button>
			</form>

			{/*If session exists, show following, if not show signed out*/}
			{session?.user ? (
				<div>Hello {session.user.name}</div>
			) : (
				<div>Signed Out</div>
			)}
		</div>
	);
}
```

How to see if a user is signed in via client component
*Requires a 'SessionProvider to be set up in the 'providers.tsx' file*

```
'use client'

import  { useSessiom } from 'next-auth/react'

export default function Profile() {
	const session = useSession()

	if (session.data?.user) {
		return <div>Signed In</div>
	} else{
		return <div>Signed Out</div>
	}
	}
}
```