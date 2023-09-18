MEOW offers some APIs:

## Ping

This API is responsible for validating the pair `endpoint` - `api_key`. It is available at `/ping/{api_key}`. This API simply searches for the client authentication registered with that `api_key` in the Redis database, returning it to the client if it exists and raising an error if it does not.

## Info

This API returns some information to the requesting client about the proceedings. It is available at `/info/{event_id}/{api_key}`. The API initially checks if the requesting client is authenticated and then returns a dictionary like this:

```json
{
  "event_id": "Conference ID",
  "pdf_cache": "true if PDF files are cached",
  "pre_press": "true if pre-press proceedings have been generated",
  "final_proceedings": "true if final proceedings have been generated",
  "datacite_json": "true if the DOI JSON payloads have been generated",
  "proceedings_archive": "true if an archive of the proceedings exists"
}
```

## Clear

This API is responsible for deleting all the folders related to a conference by event_id. It is available at `/clear/{event_id}/{api_key}`. After checking if the authentication is valid, it starts a task that deletes all the PDF files, the proceedings site, and the DOI JSON payloads.
## Websocket

This API is responsible for starting a task to which we usually refer as event. It is available at `/{api_key}/{task_id}`. After validating the credentials:

- a websocket is opened using the `task_id` sent by the client
- the task is started. In particular, `create_task` creates a thread attached to the connection that listens to the websocket messages and publishes on Redis.
- when the worker has finished, the websocket is closed.

