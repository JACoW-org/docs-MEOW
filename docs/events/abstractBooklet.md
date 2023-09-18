## List of tasks

There is a total of three tasks to be completed in order to generate the Abstract Booklet document:

1. Data collection
2. Abstract booklet data
3. ODT export

### Data collection

This task collects the data that is needed to create a conference's abstract booklet, such as sessions and contributions. The data is retrieved from Indico. At first, the list of sessions is obtained. Then for every session, MEOW is fetching the list of contributions. 
The task returns the list of sessions and contributions.

### Abstract booklet data

This task takes in input the list of sessions and contributions and organizes the data in a dictionary, that is then returned.

### ODT export

This tasks performs the creation of the ODT document, using the OdfPy library. More about it on [OdfPy](https://github.com/eea/odfpy).

Here is a list of the subtasks exploited in order to create the document, after the odt object is created:

1. Document styles are set
2. The document index is created
3. The document's table of content is created, with contributions grouped by their session
4. The document's body is created by appending the contributions' abstracts in the order defined by the table of contents
5. The ODT object is then serialized and written in a file