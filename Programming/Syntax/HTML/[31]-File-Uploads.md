[Previous](./[30]-Form-Validation-Attributes.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[32]-The-Head-Element-in-Depth.md)

*Forms*

# Lesson 31 - File Uploads

## 31.1 `<input type="file">`

`type="file"` lets users choose a file from their device to upload.

```html
<label for="resume">Upload your resume</label>
<input type="file" id="resume" name="resume">
```

---

## 31.2 `accept` and `multiple`

`accept` restricts which file types the picker shows (a hint for the user — it isn't a security guarantee, so the server must still validate uploads). `multiple` allows selecting more than one file at once.

```html
<label for="photos">Upload photos</label>
<input type="file" id="photos" name="photos" accept="image/*" multiple>

<label for="doc">Upload a document</label>
<input type="file" id="doc" name="doc" accept=".pdf,.doc,.docx">
```

---

## 31.3 `enctype` for Forms

File uploads require the form's `enctype` (encoding type) to be set to `multipart/form-data` — the default encoding can't carry binary file data.

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <label for="file">Choose a file</label>
  <input type="file" id="file" name="file">
  <button type="submit">Upload</button>
</form>
```

Forgetting `enctype="multipart/form-data"` is one of the most common reasons file uploads silently fail — the file input will appear to work, but the server receives only the filename, not the file's actual contents.

[Previous](./[30]-Form-Validation-Attributes.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[32]-The-Head-Element-in-Depth.md)
