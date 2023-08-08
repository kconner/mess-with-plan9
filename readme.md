# mess-with-plan9

## Run 9front in a virtual machine with UTM

- `brew install utm`
- Get a 9front ISO from the Mirrors section of [9front releases](http://9front.org/releases/). I chose amd64.
- Open UTM, then create a VM.
  - Emulate in my case, to run amd64 on arm64
  - Other type
  - Open the ISO
  - Use default settings, except 4GB disk should be enough
- Start the VM.
- [Install to disk](https://www.youtube.com/watch?v=yt2V7yYiPCQ)
  - Take note of which disk device file is the CD vs the hard disk.
  - On booting from the ISO, during monitor type selection: vesa got stuck, xga did not.
- After installing, `fshalt`.
- Test booting from the hard disk
  - In UTM, deselect the ISO.
  - Start the VM, log in, then `fshalt`.
- Edit the VM, name it "9front template". Clone it and use the clone thereafter.

## Now what

Hit up a search engine and let's get weird

## Findings

- The oddball scroll bar interaction is nice when you wrap your head around it: You right-click to put a line at the top or left-click to put the top at a line.
- `kbmap`
- Some things quit with the delete key; some with Control-D, some by kill, some by Rio's Delete, some by typing q.
- `/sys`
- The spirit of `games/mines`
- Like Gemini, `mothra` is delightful and probably impractical today. The experience is a bit like your browser's reader mode, except the nav and footer are present. Finally I have an explanation for sites like [cat-v.org](https://cat-v.org). It's too bad we've been able to make a living at CSS and JS.
- `/dev/mordor`
- `/lib` has pockets of discomforting culture. You really want to quote the TempleOS guy in your source tree, huh… But I came in with eyes open.
    - Same goes for some of the `games/doom` quit messages, to be honest. I guess I never played the Final Doom WADs except in source ports that must have omitted these.
        - In at least one case, two quit messages come out as one due to a missing comma. This system's C compiler must concatenate adjacent string literals in an array where the one used by id must have made them distinct items. I was able to fix, recompile, copy to /bin, verify.
- I can't get `aux/vga` to set the display mode for the life of me, including with [this gist's help](https://gist.github.com/99z/740bd7fdc020154049ebd1476f55ada2#change-resolution). Maybe something to do with the emulation environment. So I'll roll with 800x600.
    - But with that tip I did find where `plan9.ini` lives and how to edit it, so at least my my keyboard map is set at startup.
- OK, let's talk file systems and namespaces.
    - Linux's non-storage file systems like `procfs` seemed neat before, but messy. Files are messy. They're a giant tree of global variables, only somewhat controlled by access rights, not so different from the Windows Registry (do they still have that?). Not everything *should* be a file, right? Files are messes are complication.
    - In Plan 9, they really commit to files, and namespaces seem to keep it much cleaner. For instance, `envfs` exists and serves `/env` as a distinct view to each process. I can find no `env` or `set` to list them; `ls` and `cat` do that operation in general. I get the impression that `rc` literally reads `$prompt` from its view of `/env/prompt` before asking for each command.
    - `ns` shows output like Linux/macOS `mount`, but for the calling process's own view of the file system, presented as a series of `rc` commands for building the namespace up from intrinsic devices. Each line adds to, renames, or masks what came before. It's akin to printing a Lisp-2 function's stack of dynamic environment frames. You can see the state monad if you squint.
    - I thought each process would see a tightly limited namespace, but really they can each see a lot. The power and simplicity seems to be in the overriding and substitution in a uniform way, rather than in providing only what the process needs. But you could see where capability-based security might offer each process less.
- The system I dream of is somewhat like this, but not so damned imperative. At least namespacing for child processes involves mutating *a copy of* the view to the shared state.
    - I think there is a viewpoint here, as I understand it a proto-Go viewpoint, that says imperative can do all the same things more directly and extra layers are wasteful, so it's enough, just avoid bloat and especially the vertical kind.
    - Maybe systems programming wouldn't be so different from UI programming if the UI was not atop a tower. Having studied Lisp but not really lived in it, I think you could have a pyramid without the tower exactly, if you resist DSLing everything.
    - Plan 9 is not ignorant of multiprocessing; it just does it with processes and clustering. Threads *are* a hack.
        - Go came to the table with greener threads, but then, as neat as channels are, you still have to be responsible for them, and these days we want safety and neatness.
        - Erlang had that right, but also doesn't get used because it's heavy elsewhere.
        - Swift's concurrency, macro, and linear type features look like around 2025 it may be the language that systems programmers could most benefit from but least want to be associated with. In the Plan 9 world, LLVM must already be many bridges too far.
- [WhatIsNotInPlanNine](http://wiki.c2.com/?WhatIsNotInPlanNine)… Aha, I was looking for a way to solve the same problem as `find`.
