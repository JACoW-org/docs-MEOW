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

- `a` is for "add" and tells the command to create a new archive.
- `-t7z` tells the command what type of archive to use, in this case, `7z`.
- `-m0=Deflate` is the compression method.
- `-ms=16m` sets the compression dictionary size to 16MB.
- `-mmt=4` sets the maximum amount of threads to be used for the compression process to 4.
- `-bd` tells the command not to show a progress bar.
- `-mx=1` this option sets the compression level to 1, prioritizing compression speed.
- `--` is a delimiter that separates the command options from the file names.
- `{event_id}.7z` is the destination of the compressed archive to be created.
- `{event_id}` is the name of the file or folder to be compressed.

When the execution of the above command is completed, the task finishes.
