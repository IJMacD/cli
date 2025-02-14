# stats

<!---MARKER_GEN_START-->
Display a live stream of container(s) resource usage statistics

### Aliases

`docker container stats`, `docker stats`

### Options

| Name                  | Type     | Default | Description                                                                                                                                                                                                                                                                                                                                                                                                                          |
|:----------------------|:---------|:--------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-a`, `--all`         |          |         | Show all containers (default shows just running)                                                                                                                                                                                                                                                                                                                                                                                     |
| [`--format`](#format) | `string` |         | Format output using a custom template:<br>'table':            Print output in table format with column headers (default)<br>'table TEMPLATE':   Print output in table format using the given Go template<br>'json':             Print in JSON format<br>'TEMPLATE':         Print output using the given Go template.<br>Refer to https://docs.docker.com/go/formatting/ for more information about formatting output with templates |
| `--no-stream`         |          |         | Disable streaming stats and only pull the first result                                                                                                                                                                                                                                                                                                                                                                               |
| `--no-trunc`          |          |         | Do not truncate output                                                                                                                                                                                                                                                                                                                                                                                                               |


<!---MARKER_GEN_END-->

## Description

The `docker stats` command returns a live data stream for running containers. To
limit data to one or more specific containers, specify a list of container names
or ids separated by a space. You can specify a stopped container but stopped
containers do not return any data.

If you need more detailed information about a container's resource usage, use
the `/containers/(id)/stats` API endpoint.

> **Note**
>
> On Linux, the Docker CLI reports memory usage by subtracting cache usage from
> the total memory usage. The API does not perform such a calculation but rather
> provides the total memory usage and the amount from the cache so that clients
> can use the data as needed. The cache usage is defined as the value of
> `total_inactive_file` field in the `memory.stat` file on cgroup v1 hosts.
>
> On Docker 19.03 and older, the cache usage was defined as the value of `cache`
> field. On cgroup v2 hosts, the cache usage is defined as the value of
> `inactive_file` field.

> **Note**
>
> The `PIDS` column contains the number of processes and kernel threads created
> by that container. Threads is the term used by Linux kernel. Other equivalent
> terms are "lightweight process" or "kernel task", etc. A large number in the
> `PIDS` column combined with a small number of processes (as reported by `ps`
> or `top`) may indicate that something in the container is creating many threads.

## Examples

Running `docker stats` on all running containers against a Linux daemon.

```console
$ docker stats

CONTAINER ID        NAME                                    CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
b95a83497c91        awesome_brattain                        0.28%               5.629MiB / 1.952GiB   0.28%               916B / 0B           147kB / 0B          9
67b2525d8ad1        foobar                                  0.00%               1.727MiB / 1.952GiB   0.09%               2.48kB / 0B         4.11MB / 0B         2
e5c383697914        test-1951.1.kay7x1lh1twk9c0oig50sd5tr   0.00%               196KiB / 1.952GiB     0.01%               71.2kB / 0B         770kB / 0B          1
4bda148efbc0        random.1.vnc8on831idyr42slu578u3cr      0.00%               1.672MiB / 1.952GiB   0.08%               110kB / 0B          578kB / 0B          2
```

If you don't [specify a format string using `--format`](#format), the
following columns are shown.

| Column name               | Description                                                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------|
| `CONTAINER ID` and `Name` | the ID and name of the container                                                              |
| `CPU %` and `MEM %`       | the percentage of the host's CPU and memory the container is using                            |
| `MEM USAGE / LIMIT`       | the total memory the container is using, and the total amount of memory it is allowed to use  |
| `NET I/O`                 | The amount of data the container has received and sent over its network interface             |
| `BLOCK I/O`               | The amount of data the container has written to and read from block devices on the host       |
| `PIDs`                    | the number of processes or threads the container has created                                  |

Running `docker stats` on multiple containers by name and id against a Linux daemon.

```console
$ docker stats awesome_brattain 67b2525d8ad1

CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
b95a83497c91        awesome_brattain    0.28%               5.629MiB / 1.952GiB   0.28%               916B / 0B           147kB / 0B          9
67b2525d8ad1        foobar              0.00%               1.727MiB / 1.952GiB   0.09%               2.48kB / 0B         4.11MB / 0B         2
```

Running `docker stats` on container with name nginx and getting output in `json` format.

```console
$ docker stats nginx --no-stream --format "{{ json . }}"
{"BlockIO":"0B / 13.3kB","CPUPerc":"0.03%","Container":"nginx","ID":"ed37317fbf42","MemPerc":"0.24%","MemUsage":"2.352MiB / 982.5MiB","Name":"nginx","NetIO":"539kB / 606kB","PIDs":"2"}
```

Running `docker stats` with customized format on all (Running and Stopped) containers.

```console
$ docker stats --all --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}" fervent_panini 5acfcb1b4fd1 drunk_visvesvaraya big_heisenberg

CONTAINER                CPU %               MEM USAGE / LIMIT
fervent_panini           0.00%               56KiB / 15.57GiB
5acfcb1b4fd1             0.07%               32.86MiB / 15.57GiB
drunk_visvesvaraya       0.00%               0B / 0B
big_heisenberg           0.00%               0B / 0B
```

`drunk_visvesvaraya` and `big_heisenberg` are stopped containers in the above example.

Running `docker stats` on all running containers against a Windows daemon.

```powershell
PS E:\> docker stats
CONTAINER ID        CPU %               PRIV WORKING SET    NET I/O             BLOCK I/O
09d3bb5b1604        6.61%               38.21 MiB           17.1 kB / 7.73 kB   10.7 MB / 3.57 MB
9db7aa4d986d        9.19%               38.26 MiB           15.2 kB / 7.65 kB   10.6 MB / 3.3 MB
3f214c61ad1d        0.00%               28.64 MiB           64 kB / 6.84 kB     4.42 MB / 6.93 MB
```

Running `docker stats` on multiple containers by name and id against a Windows daemon.

```powershell
PS E:\> docker ps -a
CONTAINER ID        NAME                IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3f214c61ad1d        awesome_brattain    nanoserver          "cmd"               2 minutes ago       Up 2 minutes                            big_minsky
9db7aa4d986d        mad_wilson          windowsservercore   "cmd"               2 minutes ago       Up 2 minutes                            mad_wilson
09d3bb5b1604        fervent_panini      windowsservercore   "cmd"               2 minutes ago       Up 2 minutes                            affectionate_easley

PS E:\> docker stats 3f214c61ad1d mad_wilson
CONTAINER ID        NAME                CPU %               PRIV WORKING SET    NET I/O             BLOCK I/O
3f214c61ad1d        awesome_brattain    0.00%               46.25 MiB           76.3 kB / 7.92 kB   10.3 MB / 14.7 MB
9db7aa4d986d        mad_wilson          9.59%               40.09 MiB           27.6 kB / 8.81 kB   17 MB / 20.1 MB
```

### <a name="format"></a> Format the output (--format)

The formatting option (`--format`) pretty prints container output
using a Go template.

Valid placeholders for the Go template are listed below:

| Placeholder  | Description                                  |
|--------------|----------------------------------------------|
| `.Container` | Container name or ID (user input)            |
| `.Name`      | Container name                               |
| `.ID`        | Container ID                                 |
| `.CPUPerc`   | CPU percentage                               |
| `.MemUsage`  | Memory usage                                 |
| `.NetIO`     | Network IO                                   |
| `.BlockIO`   | Block IO                                     |
| `.MemPerc`   | Memory percentage (Not available on Windows) |
| `.PIDs`      | Number of PIDs (Not available on Windows)    |

When using the `--format` option, the `stats` command either
outputs the data exactly as the template declares or, when using the
`table` directive, includes column headers as well.

The following example uses a template without headers and outputs the
`Container` and `CPUPerc` entries separated by a colon (`:`) for all images:

```console
$ docker stats --format "{{.Container}}: {{.CPUPerc}}"

09d3bb5b1604: 6.61%
9db7aa4d986d: 9.19%
3f214c61ad1d: 0.00%
```

To list all containers statistics with their name, CPU percentage and memory
usage in a table format you can use:

```console
$ docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"

CONTAINER           CPU %               PRIV WORKING SET
1285939c1fd3        0.07%               796 KiB / 64 MiB
9c76f7834ae2        0.07%               2.746 MiB / 64 MiB
d1ea048f04e4        0.03%               4.583 MiB / 64 MiB
```

The default format is as follows:

On Linux:

    "table {{.ID}}\t{{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.MemPerc}}\t{{.NetIO}}\t{{.BlockIO}}\t{{.PIDs}}"

On Windows:

    "table {{.ID}}\t{{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.BlockIO}}"

