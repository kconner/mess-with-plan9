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
