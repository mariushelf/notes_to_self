# Notes to self

Notes, hints and observations I pick up during my coding time.

Some things I have tested, other things I have just picked up and noted here.

# Docker

## Security: Docker overwrites ufw firewall rules

Docker overwrites [UFW](https://help.ubuntu.com/community/UFW) firewall rules, meaning that every port you expose from Docker containers is reachable on the intranet, irrespectively of your UFW setup.

To [prevent](https://www.techrepublic.com/article/how-to-fix-the-docker-and-ufw-security-flaw/) this you can add `DOCKER_OPTS="--iptables=false"` to `/etc/default/docker` and restart Docker (`sudo systemctl restart docker`).

# numpy

* assignment to sliced arrays:
  ```python
  x = np.arrays([42,23,6,8])
  x[[1,3]] += 1
  x
  >>> array([42, 24,  6,  9])
  ```
  In the above code, a temporary array containing the element 1 and 3 is created, modified and writtenback into `x`. See [numpy doc on Indexing](https://numpy.org/doc/1.19/user/basics.indexing.html#assigning-values-to-indexed-arrays).
  

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
