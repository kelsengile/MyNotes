[Previous](./[22]-Background-Tasks-and-Threading.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[24]-Windows-Development.md)

*Concurrency*

# Lesson 23 - Async Programming for Responsive UIs

## 23.1 Async vs Threading

Threading runs work in parallel on separate OS threads; **async/await** lets a single thread juggle multiple in-progress operations (like waiting on a file read or a network response) without blocking, by yielding control back to the event loop while waiting. The two are complementary — async is about *not blocking while waiting*, threading is about *doing CPU work in parallel*.

---

## 23.2 async/await Syntax

```csharp
private async void OnDownloadClicked(object sender, EventArgs e)
{
    statusLabel.Text = "Downloading...";
    string data = await httpClient.GetStringAsync(url); // yields the UI thread while waiting
    statusLabel.Text = "Done!";
    resultView.Text = data;
}
```

```javascript
async function loadDocument(path) {
  showSpinner();
  const contents = await fs.promises.readFile(path, 'utf8');
  hideSpinner();
  return contents;
}
```

Because `await` yields control rather than blocking, the UI thread stays free to redraw and respond to input while the operation is pending.

---

## 23.3 Cancellation

Long async operations (a large download, an in-progress search) should be cancellable — the user might close the dialog or click "Cancel" before it finishes. Frameworks provide a cancellation primitive (`CancellationToken` in .NET, `AbortController` in JS) that you pass into the async call and trigger from the "Cancel" button's handler.

---

## 23.4 Error Handling in Async Code

Unhandled exceptions inside an async operation can silently disappear if not awaited or caught properly, leaving the UI stuck in a "loading" state forever. Always wrap awaited calls in `try/catch`, update the UI to reflect failure (an error message, a retry button), and avoid `async void` methods except for top-level event handlers, since their exceptions can't be caught by the caller.

[Previous](./[22]-Background-Tasks-and-Threading.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[24]-Windows-Development.md)
