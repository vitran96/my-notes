# Fetch data
- `XMLHttpRequest()` is quite cumbersome and outdated
    - Has been used in most JS libraries (Eg: jQuery)
- Fetch API is a modern replacement:
    - Provides an interface for fetching resources
    - More powerful and flexible feature set
    - Promise based

## Fetch abstractions

- Request: represents a resource request
- Response: represents the response to a request
- Headers: represents response/request headers, allowing you to query them and take different actions depending on the results
- Body: provides methods relating to the body of the response/request, allowing you to declare what its content type is and how it should be handled

## Fetch usage

Eg:

```jsx
fetch(baseURL + 'dishes')
	.then(response => response.json())
	.then(data => console.log(data))
	.catch(error => console.log(error.message));
```

Posting data

```jsx
fetch(baseURL + 'comments', {
	method: 'POST',
	body: JSON.stringify(newComment),
	headers: {
		'Content-Type': 'application/json'
	},
	credentials: 'same-origin'
}).then(...)
	.catch(...);
```

Dealing with error

```jsx
fetch(baseURL + 'dishes')
	.then(response => {
		if (response.ok) {
			return response;
		} else {
			let error = new Error('Error ' + response.status + ': ' + response.statusText);
			error.response = response;
			throw error;
		}
	},
	error => {
		var errMessage = new Error(error.mesaage);
		throw errorMessage;
	})
```

# JSDoc route

https://github.com/bvanderlaan/jsdoc-route-plugin