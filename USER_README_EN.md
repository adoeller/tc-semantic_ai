# tcsemantik for Total Commander

`tcsemantik` is a Total Commander file system plugin for searching and working
with document collections. It combines local full-text search, semantic search
using embeddings, and isolated AI actions such as document review, comparison,
and summarization.

The plugin presents the collection as a virtual file system. Your original
documents stay in their existing locations. Adding a file to the virtual
library registers its path and indexes its contents; it does not create another
copy of the original file.

## Main features

- A freely structured virtual document library inside Total Commander.
- Separate **Public** and **Classified** areas with enforced processing rules.
- Recursive indexing when files or entire folders are added.
- Semantic search that finds related meaning even when different words are
  used.
- Exact full-text search for words and word prefixes.
- A configurable semantic similarity threshold from 0 to 100 percent.
- Search scopes that can include selected virtual folders, with optional
  recursion.
- Timestamped, persistent search-result folders that can be opened and deleted.
- Duplicate detection across different file names and storage locations.
- AI-assisted review, comparison, summarization, and translation.
- Separate chat and embedding providers, including separate local and cloud
  providers.
- Configurable protection for confidential file names and paths.
- English, German, Ukrainian, and Russian user interfaces, with English as the
  fallback language.

## Requirements

### Ready-to-use installation

- 64-bit Windows 10 or Windows 11. Both Total Commander variants are
  supported, but the packaged service and settings application are 64-bit.
- Total Commander, 32-bit or 64-bit.
- The matching `tcsemantik.wfx` or `tcsemantik.wfx64` plugin library.
- `tcsemantik.exe`, `tcsemantik-dlg.exe`, the `_internal` directory, prompt
  files, and the `language` directory beside the WFX library.
- Write permission for the plugin directory. The plugin keeps its single INI
  file and virtual library catalog there.
- At least one configured embedding provider for semantic search.
- At least one configured chat provider for AI actions.

Python and Lazarus are not required when using the packaged installation.

Cloud providers require an internet connection and a valid API key. A fully
local installation instead requires a reachable Ollama or Open WebUI server
with suitable chat and embedding models. Hardware requirements then depend on
the selected models.

Exact full-text search works locally and does not require an AI provider after
the documents have been indexed. Semantic search requires embeddings when the
index is created and for each new semantic query.

## Supported document types

The current version extracts text from:

- Microsoft Word: `.docx`
- Microsoft Excel: `.xlsx`
- Microsoft PowerPoint: `.pptx`
- PDF: `.pdf`
- Plain text: `.txt`
- Markdown: `.md`
- Comma-separated text: `.csv`

PDF files must contain a usable text layer. Scanned PDFs without one are
recognized, but OCR is currently disabled by default. Password-protected,
encrypted, damaged, or unsupported files are marked as unreadable without
stopping the remaining indexing operation.

## Installation

1. Open the distribution ZIP directly in Total Commander and confirm the
   automatic plugin installation. The included `pluginst.inf` selects the WFX
   library matching Total Commander's architecture.
2. Keep all extracted files together. Do not move the WFX library, service,
   dialog program, `_internal`, prompts, INI file, or language files into
   separate directories.
3. If automatic installation is unavailable, open **Configuration > Options >
   Plugins > File system plugins** and add the matching WFX library manually.
4. Name the connection `tcsemantik` if Total Commander asks for a name.
5. Restart Total Commander after replacing an already loaded WFX library.
6. Open the `tcsemantik` file system plugin and select **Settings...**.

Both WFX variants may be distributed together. Total Commander loads the one
matching its own architecture.

## First-time configuration

Open **Settings...** in the plugin root. You can also open the properties of the
plugin root.

For every provider, configure:

- A unique provider name.
- The protocol: Ollama, Open WebUI, OpenAI-compatible, or Anthropic.
- The service address.
- The model name or OpenRouter preset.
- Its purpose: chat, embeddings, or both.
- An API key when required.
- Whether it is a local or cloud provider.
- Whether the provider is currently enabled.

Chat models generate reviews, comparisons, and summaries. Embedding models
create vectors for semantic indexing and queries. These are different tasks and
normally require different models.

The settings window separates **Servers** from **General** options. The Servers
page contains only the selected provider's connection and model fields. On the
General page, choose separate chat and embedding providers for **Classified –
local processing** and **Public – cloud processing**. The classified selectors
offer local providers; the public selectors offer cloud providers. Disabling a
provider does not delete its API key. The global **Allow cloud providers**
option must also be enabled before any cloud provider can be used.

Every document receives local embeddings. Public documents may additionally
receive embeddings from the configured public provider. A mixed or classified
search therefore remains local; the public embedding provider is queried only
when the selected search scope is entirely public.

The API-key field displays a placeholder when a key is already stored. Leaving
the field untouched preserves the key; deliberately clearing it removes the
key. API keys are not returned through the local service API.

The distributed default configuration enables cloud processing for **Public**
and selects OpenRouter with `openai/gpt-5-mini` for chat actions and
`openai/text-embedding-3-small` for semantic embeddings. Enter your own
OpenRouter API key in both provider entries before using these features. No API
key is included in the distribution. **Classified** processing remains local
regardless of this cloud default.

Use **Test connection** to verify that the provider is reachable and that its
configured model exists. The **Status...** entry in the plugin root shows the
service state, index state, document and vector counts, active defaults, cloud
permission, and full-text status.

## Virtual folder structure

The English interface has the following layout:

```text
tcsemantik\
  New search...
  Files\
    Public\
      <your folders>\
    Classified\
      <your folders>\
  Searches\
    <timestamp - query>\
  Actions\
    Review\
      Public\
      Classified\
    Compare\
      Public\
      Classified\
    Summary\
      Public\
      Classified\
  Settings...
  Status...
```

`Public` and `Classified` are fixed and cannot be deleted. Create your own
folders below either area with F7.

## Adding documents

1. Open **Files > Public** or **Files > Classified**.
2. Create any desired virtual subfolders with F7.
3. Copy files or folders into the open virtual folder with F5.
4. Total Commander passes the source paths to the plugin.
5. The plugin registers the paths and starts recursive indexing.

The originals remain where they are. The automatically managed `library.json`
catalog only maps virtual paths to those originals. Removing an entry from the
virtual library does not remove the original document.

When the same content exists at multiple locations, it is indexed once and
shown once in search results. The result columns can show the number of copies
and their other locations.

## Public and classified documents

Documents below **Public** may use a cloud provider when cloud use is enabled.
Documents below **Classified** are always processed locally.

Classification has priority over all provider selections. If the same original
or identical content is registered in both areas, it is treated as classified
everywhere. Configured confidential path rules and file-name patterns are an
additional veto, including for files placed under Public.

Unknown files that are not registered in the virtual library are treated as
confidential by default. This prevents an accidental cloud upload when the
plugin cannot establish a document's classification.

## Searching

Open **New search...** and enter a query.

### Semantic search

Semantic search compares the meaning of the query with document-section
embeddings. It can find relevant passages even when the wording differs.

Use the similarity slider to set the minimum accepted similarity. A value of
zero keeps all ranked candidates; higher values remove weaker matches. Suitable
values depend on the embedding model, so the threshold may need adjustment
after changing that model.

### Exact full-text search

Exact search uses the local word index and finds complete words and word
prefixes. Full-text indexing is enabled by default and can be disabled in
Settings. Disabling it removes only the word index; extracted text and semantic
vectors remain available. Enabling it again rebuilds the word index from the
stored document sections.

### Search scope

A query may cover all indexed documents or selected virtual folders. Enable
recursive search to include the selected folders' descendants; otherwise only
files directly inside each selected folder are searched.

Every saved search has a date and time in its name. Open its folder to inspect
the matching original files. Delete a search folder with Delete or F8 to remove
only the stored query and result list, never the source documents.

## AI actions

Open the desired folder under **Actions**, then choose **Public** for cloud
processing or **Classified** for local processing. Copy the required files into
that subfolder with F5. Completed outputs appear there as virtual Markdown
files. They can be opened with F3, copied out of the plugin, or deleted without
affecting the originals. Results made by older plugin versions without a saved
classification appear under **Classified**.

### Review

Copy one to three files into **Review > Public** or **Review > Classified**.
The built-in prompt asks for internal
contradictions, missing information, and inconsistent numbers, and requires
source quotations for every finding.

The supplied `Review` prompt uses `provider: any`. The Public action subfolder
selects the default cloud chat provider when cloud processing is enabled; the
Classified action subfolder selects a local provider. Confidential names,
configured confidential paths, and known classified copies remain local even
when submitted through Public.

Review result names contain a date/time stamp, the original file name, and the
configurable label `KI-Prüfung`, for example
`2026-09-14 221530 Report_KI-Prüfung.md`. Change the label in
`prompts\Prüfung.md`:

```yaml
filename_suffix: KI-Prüfung
```

### Compare

Copy exactly two versions into **Compare > Public** or
**Compare > Classified**. The plugin orders them by file
modification time, calculates the textual differences deterministically, and
then asks the chat model to explain their practical consequences with source
quotations.

The supplied `Compare` prompt also uses `provider: any`. If either input is
Classified or otherwise confidential, the entire comparison is forced to a
local provider. PDF comparison is text-only; formatting and layout changes are
not detected.

### Summary

Copy one document into **Summary > Public** or **Summary > Classified**. Every
document is processed as a separate,
isolated job. The model receives no search results, other documents, previous
jobs, or conversation history. Instructions found inside the source document
are explicitly treated as data rather than commands.

The target length is calculated as:

```text
target words = fixed target + source percentage × words in the original
```

The default is 300 words plus 1 percent of the original word count. Both values
are configurable. The generated file name contains a date/time stamp, the
original base name, and the `filename_suffix` configured in
`prompts\Zusammenfassung.md`.

Document content, prompt, target length, provider, model, temperature, and seed
contribute to the reproduction key stored in the result header. It identifies
the exact job configuration and is retained internally so that an existing
identical result can be reused without another model call. The key does not
itself make a model deterministic.

## Provider and cloud-selection rules

The effective provider is chosen in this order:

1. The global cloud switch.
2. Classified paths, duplicate-content classification, and confidential-name
   rules.
3. The provider requirement in the action's Markdown prompt file.
4. The enabled default provider for the required purpose.

A cloud request may be downgraded to a local provider for safety. A local-only
request is never upgraded to a cloud provider. Long cloud requests may require
an explicit confirmation before any data is sent.

## Files and storage locations

The following files stay together in the plugin directory:

```text
tcsemantik.wfx / tcsemantik.wfx64
tcsemantik.exe
tcsemantik-dlg.exe
tcsemantik.ini
library.json
prompts\Prüfung.md
prompts\Vergleich.md
prompts\Zusammenfassung.md
prompts\translate2german.md
prompt-template.md
language\de.json
language\en.json
language\uk.json
language\ru.json
```

There is only one active `tcsemantik.ini`, located beside the WFX library.
`library.json` is also kept only there.

Mutable index data, logs, generated action results, and job records are stored
under `%APPDATA%\tcsemantik` by default. The search index is reconstructible
from the original documents and `library.json`.

The three built-in prompt files and any additional prompt files are read only
from the `prompts` subdirectory beside the plugin. Markdown files elsewhere,
including this README, are ignored as actions. Use `prompt-template.md` from
the plugin root as the starting point for a custom action.

The supplied `translate2german.md` action uses `result_header: false` and
`filename_suffix: _German`. It therefore writes only the translated model
response. For example, `Report.docx` produces a result named
`2026-09-15 061303 Report_German.md`.

Set `result_header: false` in a prompt file's frontmatter when the generated
file must contain only the model response. This omits the provider, source,
timestamp, reproduction details, and separator normally written before the
response. The default is `true`; headerless output is useful for actions such
as translation.

Except for Compare, action result names follow
`date-time + original base name + filename_suffix + .md`; the original file
extension is replaced by `.md`. If a suffix does not start with an underscore
or hyphen, the service inserts an underscore. Compare retains its existing
`date-time Compare.md` naming scheme, and its `filename_suffix` value does not
change that name. `filename_suffix` belongs in each prompt file's frontmatter
and is not an INI setting.

## Localization

Select German, English, Ukrainian, or Russian in Settings. All visible plugin
names and dialog texts are loaded from external JSON files in the `language`
directory. English is used whenever a selected language file or an individual
translation is missing.

## Troubleshooting

### The plugin cannot start the service

- Confirm that `tcsemantik.exe` is beside the WFX library.
- Confirm that the complete `_internal` directory was installed.
- Check `%APPDATA%\tcsemantik\service.log`.
- Check whether another application already uses `127.0.0.1:8765`.
- Open **Status...** after correcting the problem.

### A provider is not reachable

- Use **Test connection** in Settings.
- Check the service address, model name, API key, and provider protocol.
- For local providers, confirm that Ollama or Open WebUI is running.
- For cloud providers, confirm internet access and provider availability.
- OpenRouter presets can temporarily fail when all models selected by the
  preset are rate-limited; retry later or select another appropriate preset.

### Semantic search returns no results

- Confirm that an embedding provider is enabled and selected as the default.
- Check the index and vector counts under **Status...**.
- Lower the semantic similarity threshold.
- Re-index after changing the embedding model because vectors from different
  models are not interchangeable.

### Exact search is unavailable

Enable **Full-text index for exact search** in Settings. The local word index is
then rebuilt from the already extracted sections.

### A document was not indexed

- Confirm that its file type is supported.
- Check whether it is encrypted, damaged, or larger than the configured limit.
- A scanned PDF requires OCR or a PDF containing a text layer.
- Check the status and service log for extraction errors.

### Settings or the library cannot be saved

The plugin directory must be writable by the current Windows user because the
single INI and `library.json` deliberately remain beside the WFX library. If the
plugin is installed below `Program Files`, ensure the installed plugin directory
has suitable permissions.

## Building from source

End users can skip this section. Development requires Python, a project-local
virtual environment, Lazarus/Free Pascal, and the packages listed in
`requirements-dev.txt`.

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
.\.venv\Scripts\python.exe -m pytest -q
.\.venv\Scripts\python.exe -m PyInstaller tcsemantik.spec
```

Build the 32-bit and 64-bit WFX libraries with the corresponding Lazarus build
modes and build `tcsemantik-dlg.lpi` for the dialog program. Project-related
Python commands should always use `.venv\Scripts\python.exe`.

## Current limitations

- PDF comparison is based on extracted text and ignores visual formatting.
- OCR is off by default, so image-only PDFs are not searchable.
- Legacy Office formats such as `.doc`, `.xls`, and `.ppt` are not supported.
- Semantic similarity values are model-specific and are not directly
  comparable after changing embedding models.
- Cloud output quality, determinism, capacity, and rate limits depend on the
  selected provider and model.
- Confidentiality rules protect against accidental routing mistakes; they are
  not a replacement for operating-system access control or organizational data
  handling policies.
