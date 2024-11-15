![[promise_action_diagram.png]]

![[promise_architecture_diagram.png]]

- Represents a value that may be available now, or in the future, or never
- What problems does Promise solve?
    - Solve callback hell
    - Promise can be chained
    - Can immediately return. Eg:
        - Promise.resolve(result)
        - Promise.reject(error)