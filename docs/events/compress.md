This event is responsible for compressing the previously generated proceedings website.

# List of Tasks

There are a total of two tasks to be executed, both with a lock on the database:

1. [Adapting Final Proceedings](#adapting-final-proceedings)
2. [Compress Static Site](#compress-static-site)

## Adapting Final Proceedings

This task builds and returns a `ProceedingsData` object that includes all the conference information.

## Compress Static Site

This task compresses the proceedings website into the `.7z` format. Initially, it checks if a compressed file for that conference already exists, and if so, it deletes it. Then, it launches the following command in a subprocess:

`bin/7zzs a -t7z -m0=Deflate -ms=16m -mmt=4 -bd -mx=1 -- {event_id}.7z {event_id}`

When the execution of the above command is completed, the task finishes.
