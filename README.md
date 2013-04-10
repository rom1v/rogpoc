See [Rhizome over git topic][topic].

[topic]: https://groups.google.com/forum/#!topic/serval-project-developers/D4Vt7nBd_7A

I am exploring the possibility to implement the Rhizome store (from Serval
Project) over GIT. This PoC is a wrapper of GIT plumbing commands to provide
Rhizome main commands.

It is implemented in bash, and is not intended to be executed on a phone.
There is no full-checking of user inputs, there is no encryption nor signatures.

A real implementation would be in C (in `servald`), and could store fields as
byte arrays (instead of Strings, even used for binary fields converted in
hexadecimal).

## Usage

### Create a new bundle

    rog create <service> <sender> <recipient> [<file>]

 * `service` is a String (e.g. `meshms`)
 * `sender` is the hexa-SID of sender (but you can put any String you want)
 * `recipient` is the hexa-SID of recipient
 * `file` is the filepath of the payload (empty for *stdin*)

#### Examples

    echo 'Hello' > /tmp/hello
    ./rog create meshms 1111 2222 /tmp/hello
    ./rog create meshms 2222 1111 <<< 'How are you?'
    # if /tmp/file.pdf exists
    ./rog create file 1111 '' /tmp/file.pdf

### List bundles

    rog list [<service>] [<sender>] [<recipient>]

Result are filtered by `service`, `sender` and/or `recipient`.
Prints 1 result per line, with fields seperated by ':'.

    bid:service:sender:recipient:date:length:data:

#### Examples

    ./rog list
    ./rog list meshms
    ./rog list meshms 1111 2222
    ./rog list meshms '' 2222
    ./rog list '' 1111
    ./rog list '' '' 2222

### Update a bundle

    rog update <bid> [<file>]

 * `file` is the filepath of the payload (empty for *stdin*)

*date*, *length*, *payload* and *sign* fields of the manifest are automatically
updated.

Other fields cannot be modified (*service* for example).

#### Examples

    echo 'Hello world !' > /tmp/hello
    ./rog update <bid> /tmp/hello
    ./rog update <bid> <<< 'This is my new message'

### Delete a bundle

    rog delete <bid>

### Export a manifest

    rog manifest <bid>

### Export a payload

    rog payload <bid>

#### Example

    ./rog payload <meshms_bid>           # prints a String message on stdin
    ./rog payload <file_bid> > file.pdf  # exports a pdf file
