# Windows Terminal - Close on Exit

[Home](../index) | [Up](wsl-index)

I often get an annoying error when exiting a terminal shell that the tab does not close, but instead stays open with an error message. What is happening is that the terminal is using the exit code of the previous command as the exit code of the terminal, and if this is not zero then it does not close cleanly.

You can solve this by specifying the close on exit behaviour of the terminal. Edit the terminal setting JSON file and add this to the defaults.

```
{
...

    "profiles": 
    {
        ...,
        [

            {
                "name": "RockyLinux-9-Base",
                "closeOnExit": "always"
            },

        ]
```

I prefer "always" as I do not tend to run commands as shells. As the explanation below says, you should only do this for shells you trust to always close cleanly.

See [this GitHub issue report](https://github.com/microsoft/terminal/issues/4223) for a more detailed explanation of the problem, and the [documentation](https://learn.microsoft.com/en-us/windows/terminal/customize-settings/profile-advanced#profile-termination-behavior) for other options.

---
[Home](../index) | [Up](wsl-index)
