# Path and environment variables

Sometimes you need to add files such as templates, aspects or static files (images, documents, etc.) to
your model.

CodeFluent Entities provides a “Files” folder where you can add any file. Folders and Files added to this
folder are under source control if source control is enabled:

IMAGE

Of course, you can use these files in your model, for example in instances:

IMAGE

If you are using a file which is not part of the model, instead of using an absolute path (that may
possibly work only on your computer) you should use an environment variable, for example:

```xml
<cf:pattern path="%CF_PATTERNS_PATH%\SoftFluent.Localization.xml" step="Methods" name="CodeFluent Localization Aspect" />
```

This way, every developer can configure his environment to access the right file:

IMAGE

Note: When you select any file in your computer, CodeFluent Entities tries to find a “portable path”
using this fixed set of environment variable automatically: CF_TEMPLATES_PATH,
CF_PATTERNS_PATH, CF_CURRENT_PATH, CommonProgramFiles, CommonProgramW6432,
ProgramFiles(x86), ProgramFiles, ProgramW6432, Windir, USERPROFILE.
