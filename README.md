# Notes to self

Notes, hints and observations I pick up during my coding time.

Some things I have tested, other things I have just picked up and noted here.

# Free disk space on ubuntu (2021-01-21)

System:
``` bash
sudo apt clean  # clean apt cache
sudo journalctl --vacuum-time=10d  # clean old journal files (here: older than 10 days)
sudo apt autoremove --purge  # remove packages that are not needed anymore (e.g., old kernels)
```

Home:
- find and delete unused Python virtual environments. Get a list with `find -name "?venv"` (finds `venv` and `.venv`)
- obviously, clean the Trash, Downloads folder, tmp folder
- go through `~/.cache` and see what you can delete

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
