![[Pasted image 20240206161629.png]]

[[NextJS]]
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