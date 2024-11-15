![[mvc_architecture_diagram.png]]

Aims:

- Isolation of domain logic from user interface
- Permit independent development, testing and maintenance

# Model

- Manage behavior and data of application domain
- Respond to request for info about its state (usually from the view)
- Respond to instruction to change state (usually from the controller)
- In event-driven system, the model notify observers (usually view) when the info change so that they can react

# View

- Render the model into a interactable form to a user
- Multiple views can exist for a single model for different purposes
- A view port typically has a 1-1 correspondence with a display surface and know how to render to it.

# Controller

- Receive user input and initiates a response by making calls on model objects
- A controller accepts input from the users and instructs the model and viewport to perform actions based on that input.

# Note From FB

Source: [https://youtu.be/nYkdrAPrdcw](https://youtu.be/nYkdrAPrdcw)

%% TODO: this is a very interesting point, require more research %%
- MVC doesn't scale