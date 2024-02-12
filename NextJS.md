[[Typescript]] [[MongoDB]]
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

When handling forms, you can use the library zod to help with error handling and checking validity
```
'use server'
import { z } from 'zod';

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
```