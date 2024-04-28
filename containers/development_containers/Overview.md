# What are development containers?
As containerizing production workloads becomes commonplace, more developers are using containers for scenarios beyond development, including continuous integration, test automation, and even full-featured coding environments.

Each scenario's needs can vary between simple single container environments to complex, orchestrated multi-container setups. Rather than attempting to create another orchestrator format with metadata or common development specific settings, tools and configuration.

# A structured metadata format
Like the [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) before it, the first format in the specification, [devcontainer.json](https://containers.dev/implementors/json_reference/) , was born out of necessity. It is a structured JSON with Comments (Jsonc) metadata format tools can use to store any needed configuration required to develop inside of local or cloud-based containerized coding.

Since the spec was initally published, dev container metadata can now be stored in the [image labels](https://containers.dev/implementors/spec/#image-metadata) and in reusable chucks of metadata and install scripts known as Dev Container Features. We envision that this same structured data can be embedded in other formats all while retaining a common object model for consistent processing. 
(Doing...)


[Dev Container Microsoft base Images](https://mcr.microsoft.com/en-us/catalog?search=devcontainer&type=partial)
[Dev Container Community Base Image](https://containers.dev/templates)
[Dev Container Features](https://containers.dev/features)
https://containers.dev/collections