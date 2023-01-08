---
title: "Subscribe"
date: 2022-12-30T21:47:47+02:00
draft: false
---

Hey, a few notes about the "subscribe" mechanism. 

I know you might find it a little strange, but I decided to run this blog completely cost-free, including the subscription mechanism. 

I explained in-depth why in this [article](https://costica.dev/posts/hacking-a-free-blogging-system-with-emails), but here's a summary of how it works: 

1. If you do decide to "subscribe", click the button below and register in the Firebase-Ui popup. The password doesn't matter, just input anything longer than 6 characters. 
2. ✉️ Please verify your email (Firebase will handle this automatically, you should get an email to the address that you chose to subscribe with). 
3. Your email will be stored in the `Firebase`'s system (for free!).
4. At times, I might decide that I wrote a post interesting enough so that I email the subscribers about it.

❗ A few caveats to this system: 

1. I won't have an email automatically sent every time I post something. It doesn't make sense for me. You'd hate me for it. Worse, I'd hate myself for it.
2. I can't spam, even if I wanted to, because I would have to write something and send it. I'm waaaay too lazy for that, I promise.
3. Unsubscribing is not supported out of the box, yet, maybe never will be. That means you have to [contact me](https://costica.dev/about-me) and let me know that I should delete your email from that list. 

Still want to go for it? 

{{< firebase_load_library >}}
{{< firebase_subscribe >}}

Yes, I'm a CSS person, as I'm sure you noticed. /s
