MEOW consists of two software components:

- **MEOW webapp**, which serves as the Web Application Interface.
- **MEOW worker**, which is the computational component.

Communication between these two components occurs through Redis PUB/SUB channels.

## MEOW Webapp

The webapp component is responsible for providing a Web API that allows clients to:

- Submit new tasks
- Retrieve information about the progress status of tasks
- Retrieve the results of a task
- Obtain information about the assets produced by tasks

## MEOW Worker

The worker component offers a list of events and is responsible for initiating them, routing information about their progress and results, and reporting errors.
