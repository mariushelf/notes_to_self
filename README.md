# Notes to self

Notes, hints and observations I pick up during my coding time.

Some things I have tested, other things I have just picked up and noted here.

# Shell config

* [Liquid Prompt](https://github.com/liquidprompt/liquidprompt): useful shell prompt with git branch, venv, ... for Bash and ZShell.
* [direnv](https://direnv.net/) to automatically load `.env` files and even activate python environments when you cd into the folder.
  * to load `.env` files (and not only `.envrc`):
```shell
cat ~/.config/direnv/direnv.toml
[global]
load_dotenv = true
```

# MacOS tools: things that make MacOS usable

* [RunCat](https://kyome.io/runcat/index.html?lang=en): system usage indicator with a cute cat
* [AltTab](https://alt-tab-macos.netlify.app/): turn the useless MacOS task switcher into a usable list of all windows
* [Bluesnooze](https://github.com/odlp/bluesnooze): turn Bluetooth off when the system sleeps such that the sleeping Macbook does not hijack the connection to your headphones
* [Amphetamine](https://apps.apple.com/nl/app/amphetamine/id937984704?mt=12): keep your Mac awake and prevent it from sleeping while you run a long-running task
* [Be Focused](https://apps.apple.com/us/app/be-focused-focus-timer/id973130201): pomodoro timer
* [HammerSpoon](https://www.hammerspoon.org/): essential keyboard shortcuts and customization for keyboard lovers. Here's my configuration:
```
-- hs.hotkey.bind({"ctrl", "cmd"}, "T", function()
--     terminal = hs.application.get("Terminal")
--     if terminal ~= nil
--     then
--         terminal:activate()
--         hs.eventtap.keyStroke({"cmd"}, "N")
--     else
--         hs.application.open("Terminal")
--     end
-- end)

hs.hotkey.bind({"ctrl", "cmd"}, "L", function() 
    hs.eventtap.keyStroke({"ctrl", "cmd"}, "Q") 
end)

hs.hotkey.bind({"alt"}, "Right", function()
    hs.window.animationDuration=0
    local win = hs.window.focusedWindow()
    local f = win:frame()
    local screen = win:screen()
    local max = screen:frame()

    f.x = max.x + (max.w / 2)
    f.y = max.y
    f.w = max.w / 2
    f.h = max.h
    win:setFrame(f)
end)

hs.hotkey.bind({"alt"}, "Left", function()
    hs.window.animationDuration=0
    local win = hs.window.focusedWindow()
    local f = win:frame()
    local screen = win:screen()
    local max = screen:frame()

    f.x = max.x
    f.y = max.y
    f.w = max.w / 2
    f.h = max.h
    win:setFrame(f)
end)

-- hs.hotkey.bind({"alt"}, "Up", function()
--     hs.window.animationDuration=0
--     local win = hs.window.focusedWindow()
--     local f = win:frame()
--     local screen = win:screen()
--     local max = screen:frame()
-- 
--     f.x = max.x
--     f.y = max.y
--     f.w = max.w
--     f.h = max.h / 2
--     win:setFrame(f)
-- end)

-- hs.hotkey.bind({"alt"}, "Down", function()
--     hs.window.animationDuration=0
--     local win = hs.window.focusedWindow()
--     local f = win:frame()
--     local screen = win:screen()
--     local max = screen:frame()
-- 
--     f.x = max.x
--     f.y = max.y + (max.h / 2)
--     f.w = max.w
--     f.h = max.h / 2
--     win:setFrame(f)
-- end)

hs.hotkey.bind({"alt"}, "Up", function()
    hs.window.animationDuration=0
    local win = hs.window.focusedWindow()
    local f = win:frame()
    local screen = win:screen()
    local max = screen:frame()

    f.x = max.x
    f.y = max.y
    f.w = max.w
    f.h = max.h
    win:setFrame(f)
end)

hs.hotkey.bind({"alt"}, "Down", function()
    hs.window.animationDuration=0
    local win = hs.window.focusedWindow()
    local f = win:frame()
    local screen = win:screen()
    local max = screen:frame()

    f.x = max.x + max.w * .25
    f.y = max.y + max.h * .25
    f.w = max.w * 0.5
    f.h = max.h * 0.5
    win:setFrame(f)
end)


function moveToNextScreen()
    hs.window.animationDuration=0
    local app = hs.window.focusedWindow()
    app:moveToScreen(app:screen():next())
    -- app:maximize()
end
hs.hotkey.bind({"alt"}, "k", moveToNextScreen)
```

# Profiling and Benchmarks

- General purpose CLI benchmarking: [Hyperfine](https://github.com/sharkdp/hyperfine)
- Python memory profiling: [memory_profiler](https://github.com/pythonprofilers/memory_profiler). Can create nice graphs of memory usage over time.

# Free disk space on ubuntu (2021-01-21)

System:
``` bash
sudo apt clean  # clean apt cache
sudo journalctl --vacuum-time=10d  # clean old journal files (here: older than 10 days)
sudo apt autoremove --purge  # remove packages that are not needed anymore (e.g., old kernels)
docker system prune --all  # delete all unused docker resources. Use with care
```

Home:
- find and delete unused Python virtual environments. Get a list with `find -name "?venv"` (finds `venv` and `.venv`)
- go through old projects and remove obsolete data
- obviously, clean the Trash, Downloads folder, tmp folder
- go through `~/.cache` and see what you can delete
- limit the Spotify download cache: close Spotify, add `storage.size=1024` to `~/Library/Application Support/Spotify/prefs` and open Spotify again
- clear the Steam download cache via `Settings` -> `Downloads`

In general:
- [QDirStat](https://github.com/shundhammer/qdirstat) is a nice tool to visually find the biggest files and folders on your disk


# Docker

## Security: Docker overwrites ufw firewall rules

Docker overwrites [UFW](https://help.ubuntu.com/community/UFW) firewall rules, meaning that every port you expose from Docker containers is reachable on the intranet, irrespectively of your UFW setup.

To [prevent](https://www.techrepublic.com/article/how-to-fix-the-docker-and-ufw-security-flaw/) this you can add `DOCKER_OPTS="--iptables=false"` to `/etc/default/docker` and restart Docker (`sudo systemctl restart docker`).

To then allow a certain port again, run e.g. `sudo ufw allow <port_number>`, e.g, `sudo ufw allow 27017` for the mongodb default port.

# Enums

Python-s built-in [enumerations](https://docs.python.org/3/library/enum.html) are very handy to define constants, however they are super slow.
Use the [FastEnum](https://github.com/QratorLabs/fastenum) implementation instead.

On a project with ~100,000 iterations in a loop and a few enum comparisons in each iteration I could achieve a 10% performance improvements by switching to the FastEnum implementation.

**Note**: make sure to explicitly cast types. `FastEnum`s declared with a certain type do not compare to other types: 

```python
class MyEnum(int, metaclass=FastEnum)
    A = 1

a = MyEnum.A
assert a == 1    # succeeds
assert a == 1.0  # fails
```

# numpy

* assignment to sliced arrays:
  ```python
  x = np.arrays([42,23,6,8])
  x[[1,3]] += 1
  x
  >>> array([42, 24,  6,  9])
  ```
  In the above code, a temporary array containing the element 1 and 3 is created, modified and writtenback into `x`. See [numpy doc on Indexing](https://numpy.org/doc/1.19/user/basics.indexing.html#assigning-values-to-indexed-arrays).

* slicing arrays with an actual slice is much faster than slicing by a list ("fancy slicing"), because the latter creates a copy, while slicing by slice creates a view: [numpy docs](https://scipy-cookbook.readthedocs.io/items/ViewsVsCopies.html#I-think-I-understand-what-a-view-is,-but-why-fancy-indexing-is-not-returning-a-view?):
  ```python
  a = np.random.random([100, 100])
  x = a[:5]  # this is fast
  x = a[[0,1,2,3,4]]  # this is about factor 8 to 10 slower
  ```
  

# pandas

* the category datatype can save memory and computation time on both text and numeric columns with repeating values. Even joins and filters are faster
* using `float32` or `int32` instead of the default 64 datatypes can make operations faster and memory usage lighter

## Faster numerical expressions in pandas series and dataframes

Pandas can make use of [`bottleneck`](https://github.com/pydata/bottleneck) and [`numexpr`](https://github.com/pydata/numexpr) to speed up computations.

When installed, pandas will use them automatically. Explicit configuration whether to use either via pandas options `compute.use_bottleneck` and `compute_use_numexpr` (both defaulting to True).

# Python performance

## compile python with optimization:

```bash
PYTHON_CFLAGS=-march=native CONFIGURE_OPTS='--enable-optimizations --with-lto' pyenv install 3.x.y
```

Docs: [Performance options](https://docs.python.org/3/using/configure.html#performance-options)

## numpy
* `bottleneck` has some nice high-performance vector operations

## asyncio
* `uvloop` provides a significantly faster event loop. Usage:
  `pip install uvloop`, and in the code: `import uvloop; uvloop.install(); asyncio.run(mymain())`


# Dummy Data

* [faker](https://faker.readthedocs.io/en/latest/) looks good to create dummy data
* [Factory Boy](https://factoryboy.readthedocs.io/en/latest/) looks interesting to generate dummy data and mock data in combination with a database structure. It uses faker under the hood. Supports django and sqlalchemy ORMs out of the box.

# git

## Bash completion in Ubuntu

If bash completion does not work after installation (or on some server systems), you need to source the respective file like so:

`source /usr/share/bash-completion/completions/git`

You can of course also add that to your `.bashrc`.


## Remove files from history

To remove files from the history (e.g., those old Jupyter notebooks that are huuuge, but no one will ever look at again), [BFG Repo Cleaner](https://github.com/rtyley/bfg-repo-cleaner) seems to be the tool of choice.
