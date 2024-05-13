[[Typescript]] [[MongoDB]]
If youre using useRouter(), make sure its imported from next/navigation, router refresh reruns the route you're on and refreshes it client side.
```
<UploadButton endpoint="imageUploader" onClientUploadComplete={() => {router.refresh}} />
```
when upload is completed, it refreshes the page for the user.

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


![[Pasted image 20240209135900.png]]
What makes a page dynamic?
```
- Calling a dynamic function or refencing a dynamic variable when your route renders.
	- cookies.set()/cookies.delete()
	- useSearchParams()/searchParams prop
- Assigning specific route segment config options
	- export dynamic = 'force-dynamic'
	- export const revalidate = 0
- Calling 'fetch and optng out of caching of the responsr
	- fetch( '...', {next: { revalidate: 0 }});
```

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

When handling forms, you can use the library zod to help with error handling and checking validity
```
'use server'
import { z } from 'zod';
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

Here is a standard forum component that can be broken down and reused.
```
'use server';
import { z } from 'zod';
import { auth } from '@/app/auth';
import type { Topic } from '@prisma/client';
import { redirect } from 'next/navigation';
import { paths } from '@/paths';
import { db } from '@/db';
import { revalidatePath } from 'next/cache';

const createTopicSchema = z.object({
	name: z
		.string()
		.min(3)
		.regex(/^[a-z-]+$/, {
			message: 'Only lowercase letters and hyphens are allowed',
		}),
	description: z.string().min(10),
});

export async function createTopic(formData: FormData) {
	const result = createTopicSchema.safeParse({
		name: formData.get('name') as string,
		description: formData.get('description') as string,
   });

	if (!result.success) {
		console.log(result.error.flatten().fieldErrors);
   } //flatten() is from zod to shorten the errors.
   
}

```

when using useFormState from 'react-dom' you will get an error when making your formstate.
```
const [formState,action] = useFormState(serverArction, ___ )
```
![[Pasted image 20240210205408.png]]

To fix this error, go into your server action file to make a new interface, add a formState param with that interface, and use a promise with the form state. This turns the function asynchronous 
```

		.regex(/^[a-zA-Z-]+$/, {
			message: 'Only letters and hyphens are allowed',
		}), //message needed on regex because it is a custom error message
	description: z.string().min(10),
});

interface CreateTopicFormState {
	errors: {
		name?: string[];
		description?: string[];
	};
}

export async function createTopic(
	formState: CreateTopicFormState,
	formData: FormData,
): Promise<CreateTopicFormState> {
	const result = createTopicSchema.safeParse({
		name: formData.get('name') as string,
		description: formData.get('description') as string,
	});

	if (!result.success) {
		console.log(result.error.flatten().fieldErrors);
		return { errors: result.error.flatten().fieldErrors };
	}
	return {
		errors: {},
	};
}

	const session = await auth();
	if (!session || !session.user) {
		return {
			errors: { _form: ['You must be signed in to create a topic'] },
		};
	}

	let topic: Topic;
	try {
		topic = await db.topic.create({
			data: {
				slug: result.data.name,
				description: result.data.description,
			}
		});
	} catch (error: unknown) {
		if (error instanceof Error) {
			return {
				errors: { _form: [error.message] },
			};
		} else {
			return {
				errors: { _form: ['An unknown error occurred'] },
			};
		}
	}

	revalidatePath('/');
	redirect(paths.topicShow(topic.slug));
}

```

Code for a form-button module, includes loading boolean to make a spinner icon.
```
'use client';
import { useFormStatus } from 'react-dom';
import { Button } from '@nextui-org/react';

interface FormButtonProps {
	children: React.ReactNode;
}

export default function FormButton({ children }: FormButtonProps) {
	const { pending } = useFormStatus();

	return (
		<Button
			type='submit'
			color='primary'
			variant='flat'
			isLoading={pending}>
			{children}
		</Button>
	);
}

```