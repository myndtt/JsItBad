jsitbad(1) -- classify .js files as malicious or legitimate
===========================================================


## SYNOPSIS
`jsitbad` `add` [-f] [(-m|-l|-u) [message]] `database` `js-file`

`jsitbad` `fit` [-o `output-file`] `database`

`jsitbad` `predict` (-f `hash-file`|`hash` ...| `js-file` ...) [-o `json-file`] -i (`database`|`training`)

## DESCRIPTION

`jsitbad` tries to assess the malignity of JavaScript snippets (or whole files). Details about the algorithm should appear in a paper that I have yet to write.

The `add` command adds a snippet in the `database` (See section [DATABASE][]). If no message is specified, a default one is constructed from the username, hostname and the date. The `message` argument should include information about the file (e.g. its origin), its malignity (e.g. who did the assessment, what is the modus operandi, how certain are we about the assessment) etc.

`add`-ing a snippet that already exists in the database is not always an error:

* If the snippet's status is the same as the given `-m`, `-l` or `-u` flag, the metadata of the snippet is updated to reflect the fact that the snippet was seen again.
* If the existing snippet's status is different from the given flag and no `message` is provided, the command will always fail.
* If the existing snippet's status is unknown, and the `-m` or `-l` flag is provided, the snippet is moved to the appropriate subdirectory and its metadata is updated accordingly.
* If the existing snippet's status is malicious or legitimate, its status will change only if the `-f` flag is provided.

The `fit` command trains the classifer on the specified `database`. Information about the training, such as various assessments of the performance will be printed on `stdout` or in `output-file` if specified. Some files will be written in a subdirectory of the `database` directory, to be used by `predict`. The name of the subdirectory is of the form `YYYYMMDDHHSS_training`. It is untested if files in this directory can be used across machines. See the [BUGS][] section about that.

For vizualization purposes, the `fit` command creates a .svg file in the training directory, which contains all the samples as dots and the classifier output as color zones.

The `predict` command tells the user whether some snippets are malicious or legitimate. The snippets are referenced either by their sha1 hash (either one per line in a file if the `-f` option is used, or in a space separated list on the command line) or by their filename (one snippet per file).

`predict` first checks if the file exists in the database. If it does and its metadata is up to date, it will output that without computing anything. If the file does not exist or if its metadata is obsolete, `predict` will run the latest classifier in `database` (or alternatively the classifier in the user-specified `training` directory) on the snippet and output (either on `stdout` or on the file specified with the `-o` option) one JSON object per snippet.

If a snippet given to `predict` is not already in the `database`, `predict` will be add it, the same way calling `add -u` would.

This object contains information about the classification, mainly an assessment of the file ('malicious' or 'legitimate'), a classification score (a float between 0 and 1, closest to 1 if the file looks malicious), information about vizualization (e.g. coordinates in a 2D plot).

This object is appended by `predict` to the .json file that contains the metadata of the snippet.

## OPTIONS

`add` command:

* `-f` force change : allow the user to change a snippet status from malicious or legitimate
* `-m` mark the file as malicious
* `-l` mark the file as legitimate
* `-u` (default) mark the file's status as unknown

`fit` command:

* `-o` specify an output file, defaults to `stdout` if unspecified

`predict` command:

* `-f` specify a file to read hashes from, one hash per line
* `-o` specify an output file, defaults to `stdout` if unspecified
* `-i` specify which database or training directory to use

## DATABASE

`jsitbad`'s database consists of three directories :

* `malicious`: malicious files
* `legitimate`: legitimate files
* `unknown`: files of unknown status

Each of those directories contains the JavaScript files, whose name is the sha1 checksum of the code inside with the `.js` extension (e.g. `ffabce78341dc27fc7993efadd46fa54fbcb55dc.js`).

Every JavaScript file should be accompanied by a `.json` file that bears the same name except for the extension and holds metadata about the JavaScript snippet. Such metadata can include the user-specified or machine-generated  message at the time of addition, information about previous runs of `predict` and `fit` and information about occurences of this snippet in the wild.

## SYNTAX
## ENVIRONMENT
## RETURN VALUES
## STANDARDS
## SECURITY CONSIDERATIONS
## BUGS

* The JSON schema for the metadata and the output of `predict` is not written, although this may be a feature and not a bug after all.
* It is unknown whether the files written by `fit` are portable across machines. Training directories should therefore not be sent, transferred nor saved to version control. Instead, the database should be transferred and the `fit` command run again.

## HISTORY
## AUTHOR

Joint work @sekoia.fr

## COPYRIGHT
## SEE ALSO



## DESCRIPTION

## TODO


