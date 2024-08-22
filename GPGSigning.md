**NOTE:** This fix is for macOS.

If GPG throws this error when you try to commit your code:

```sh
error: gpg failed to sign the data:
[GNUPG:] KEY_CONSIDERED <YOUR-KEY> 2
[GNUPG:] BEGIN_SIGNING H8
gpg: signing failed: No pinentry
[GNUPG:] FAILURE sign <SOME NUMBERS>
gpg: signing failed: No pinentry

fatal: failed to write commit object
```

follow these steps:

1. run `brew install pinentry-mac`
2. then `which pinentry-mac | pbcopy`. You will have a path like this in your clipboard: ***/opt/homebrew/bin/pinentry-mac***
3. put that path in ***gpg-agent.conf*** file. Create the file if it does not exist. You can easily add the path by running this command:
   - `echo "pinentry-program /opt/homebrew/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf`
  
4. finally, run `gpgconf --kill gpg-agent`
5. test that GPG signing now works by running this command and checking that it runs successfully:
   - `echo "test" | gpg --clearsign`
  
6. If these steps do not solve your GPG signing issue, try other solutions from [this](https://superuser.com/questions/1628782/gpg-signing-failed-no-pinentry) StackExchange QA, or others on the internet.
