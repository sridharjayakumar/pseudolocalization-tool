# Introduction #

Here is some sample code showing how to use the pseudolocalization library.


# Plain Text #

```
String text;
PseudolocalizationPipeline pipeline = PseudolocalizationPipeline.getVariantPipeline("psaccent");
String result = pipeline.localize(text);
```

# Structured Messages #

```
Message msg = buildStructuredMessage();
PseudolocalizationPipeline pipeline = PseudolocalizationPipeline.getVariantPipeline("psbidi");
pipeline.localize(msg);
// use the localized msg
```

# Command Line #
You can also just use the command-line tool to run the pseudolocalizer:

```
pseudolocalizer [--ext=fqcn[,fqcn...]] [--variant=varname|--method=method[,method...] [--type=filetype]
  [<--interactive|files>]
```

  * `--ext` loads additional classes by the fully qualified class name (expected to be on the classpath), either supporting additional file types or pseudolocalization methods.  The static initializer is expected to register them appropriately.
  * Either `--variant` or `--method` is used to specify the pseudolocalization to be performed; if not present it is treated as if the variant `psaccent` was supplied.
  * `--type` gives the type of file to be used, and defaults to the extension of the file if not present
  * `--interactive` says to read strings to localize from the console, and is useful if you don't have a file format  implementation or just want to experiment.  If not present, the a list of files to localize must be supplied.  In that case, the output will be written to `<file>_<variant>.<ext>` (if `--method` is used instead, `<variant>` will be "pseudo").