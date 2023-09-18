MEOW offers some events that are responsible for handling the DOIs of a conference. These services use [DataCite APIs](https://support.datacite.org/reference/get_dois).

A DOI can be in the following states:

- *findable*
- *registered*
- *draft*

A DOI in the *findable* state is published.
A DOI in the *registered* state is a DOI that has been officially registered in the DataCite repository with a permanent and unique identifier.
A DOI in the *draft* state has just been created and is not fully registered, which means its identifier and metadata can still be edited.

The operations that are handled are the following:

- **DOI info fetching**: Retrieves the states of all DOIs in the conference.
- **DOI drafting**: Submits the metadata of every contribution in the conference to create the DOIs in the *draft* state.
- **DOI publishing**: Attempts to publish the DOIs of the conference, switching them to the *findable* state.
- **DOI deletion**: Attempts to delete the DOIs of the conference, possible only if they are in the *draft* state.
- **DOI hiding**: Attempts to change the state of the DOIs of the conference from *findable* to *registered*.

All the previous events follow the typical workflow of an event in MEOW, which involves a list of tasks.

## Proceeding Object Building

This set of tasks includes those responsible for building an instance of `ProceedingsData` object. It is common to all DOI events:

- Retrieving data of sessions and attachments from Indico.
- Retrieving data of contributions from Indico.
- Combining the data into a single `ProceedingsData` object.

## Event Feature Tasks

### DOI Info Fetching

This task retrieves all the DOI payloads from the folder `var/run/{event_id}_doi`, and for every contribution, it uses the `GET https://api.test.datacite.org/dois/{id}` API to retrieve the metadata and therefore the state.

### DOI Drafting

This task retrieves all the DOI payloads from the folder `var/run/{event_id}_doi`, and for every contribution, it uses the `PUT https://api.test.datacite.org/dois/{id}` API request to submit the metadata, which, in the default case, results in the creation of the DOI in the *draft* state.

### DOI Publishing

This task works like the [DOI Drafting](#doi-drafting) task, but before making the PUT request, the attribute `event` of the payload is changed to `publish`.

### DOI Deletion

This task retrieves all the DOI payloads from the folder `var/run/{event_id}_doi`, and for every contribution, it uses the `DELETE https://api.test.datacite.org/dois/{id}`. This task works only for DOIs in the *draft* state.

### DOI Hiding

This task works like the [DOI Drafting](#doi-drafting) task, but before making the PUT request, the attribute `event` of the payload is changed to `hide`. This task works only to change the state of DOIs in the `findable` state to `registered`.
