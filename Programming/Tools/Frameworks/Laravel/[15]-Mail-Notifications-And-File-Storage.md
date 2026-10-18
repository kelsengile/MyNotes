[Previous](./[14]-Queues-Events-And-Scheduling.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[16]-Building-APIs-With-Laravel.md)

*Application Features*

# Lesson 15 - Mail, Notifications, And File Storage

## 15.1 Sending mail

Mail settings are in `.env`:

```env
MAIL_MAILER=smtp
MAIL_HOST=sandbox.smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your-username
MAIL_PASSWORD=your-password
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"
```

For development, `MAIL_MAILER=log` writes emails into `storage/logs/laravel.log`. A sandbox service like Mailtrap catches real SMTP messages without delivering them. Production options include SMTP, Postmark, Resend, Amazon SES, and Mailgun.

Each email is a **Mailable** class:

```bash
php artisan make:mail OrderShipped
```

```php
<?php

namespace App\Mail;

use App\Models\Order;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class OrderShipped extends Mailable
{
    use Queueable, SerializesModels;

    public function __construct(public Order $order) {}

    public function envelope(): Envelope
    {
        return new Envelope(subject: 'Your order has shipped');
    }

    public function content(): Content
    {
        return new Content(view: 'emails.orders.shipped');
    }
}
```

The view (`resources/views/emails/orders/shipped.blade.php`) can use `$order` because it is a public property.

Send it:

```php
use Illuminate\Support\Facades\Mail;

Mail::to($user)->send(new OrderShipped($order));
Mail::to($user)->cc($manager)->queue(new OrderShipped($order)); // via the queue
```

Add attachments through an `attachments()` method returning `Attachment::fromPath(...)`. For attractive responsive emails without writing HTML, generate a Markdown mailable with `php artisan make:mail OrderShipped --markdown=emails.orders.shipped`.

---

## 15.2 Notifications

**Notifications** are short messages sent through one or more channels: email, database, SMS, Slack, push, and more. A single notification class can target several channels.

```bash
php artisan make:notification InvoicePaid
```

```php
<?php

namespace App\Notifications;

use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class InvoicePaid extends Notification
{
    public function __construct(public $invoice) {}

    public function via(object $notifiable): array
    {
        return ['mail', 'database'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject('Invoice paid')
            ->line('Your invoice has been paid.')
            ->action('View invoice', url('/invoices/' . $this->invoice->id))
            ->line('Thank you!');
    }

    public function toArray(object $notifiable): array
    {
        return ['invoice_id' => $this->invoice->id];
    }
}
```

Send it:

```php
$user->notify(new InvoicePaid($invoice));          // User uses the Notifiable trait

use Illuminate\Support\Facades\Notification;
Notification::send($users, new InvoicePaid($invoice)); // many users
```

**Database notifications** are stored in a `notifications` table. Create it first:

```bash
php artisan make:notifications-table
php artisan migrate
```

Read them:

```php
$user->unreadNotifications;       // collection
$user->notifications;             // all
$user->unreadNotifications->markAsRead();
```

Add `implements ShouldQueue` and `use Queueable;` to a notification class to send it in the background. Mail is the difference to remember: Mailables are for one dedicated email, while Notifications are for messages that may go to several channels.

---

## 15.3 Filesystem disks

Laravel's storage layer (`Storage` facade) works the same across local folders and cloud services. Disks are configured in `config/filesystems.php`, and the default is chosen with `FILESYSTEM_DISK` in `.env`.

| Disk | Location | Use |
|---|---|---|
| `local` | `storage/app/private` | Private files, not publicly reachable |
| `public` | `storage/app/public` | Files meant for the web |
| `s3` | Amazon S3 (or compatible) | Cloud storage (`composer require league/flysystem-aws-s3-v3 "^3.0"`) |

To make the `public` disk reachable by URL, create the symbolic link once:

```bash
php artisan storage:link
```

This links `public/storage` to `storage/app/public`.

Basic operations:

```php
use Illuminate\Support\Facades\Storage;

Storage::put('notes/readme.txt', 'Hello');            // default disk
Storage::disk('public')->put('logo.png', $contents);

Storage::get('notes/readme.txt');
Storage::exists('notes/readme.txt');
Storage::delete('notes/readme.txt');
Storage::copy('a.txt', 'b.txt');
Storage::move('a.txt', 'archive/a.txt');
Storage::size('a.txt');
Storage::files('notes');          // files in a directory
Storage::makeDirectory('exports');

Storage::disk('public')->url('logo.png');             // public URL
Storage::temporaryUrl('private.pdf', now()->addMinutes(10)); // supported on S3
Storage::download('report.pdf');                      // download response
```

Because every disk shares the same API, you can develop locally and switch to S3 in production by changing configuration only.

---

## 15.4 File uploads

Form (note `enctype`):

```blade
<form method="POST" action="{{ route('avatar.store') }}" enctype="multipart/form-data">
    @csrf
    <input type="file" name="avatar">
    @error('avatar') <p>{{ $message }}</p> @enderror
    <button type="submit">Upload</button>
</form>
```

Controller:

```php
public function store(Request $request)
{
    $request->validate([
        'avatar' => ['required', 'image', 'mimes:jpg,jpeg,png,webp', 'max:2048'], // KB
    ]);

    $path = $request->file('avatar')->store('avatars', 'public');
    // e.g. avatars/SomeRandomName.jpg  (a unique name is generated)

    $request->user()->update(['avatar_path' => $path]);

    return back()->with('status', 'Avatar updated!');
}
```

Other useful calls:

```php
$file = $request->file('avatar');
$file->getClientOriginalName();
$file->getClientOriginalExtension();
$file->getSize();
$file->storeAs('avatars', $request->user()->id . '.jpg', 'public'); // choose the name
```

Showing the file:

```blade
<img src="{{ Storage::disk('public')->url($user->avatar_path) }}" alt="Avatar">
```

Security tips:

- Always validate file type and size. Do not trust the client file name or extension.
- Store the generated path in the database, not the file contents.
- Keep sensitive documents on a private disk and serve them through a controller that checks authorization.
- Also raise PHP's `upload_max_filesize` and `post_max_size` if you allow large files.

[Previous](./[14]-Queues-Events-And-Scheduling.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[16]-Building-APIs-With-Laravel.md)