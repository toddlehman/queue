## Name
`queue` — a parallel job scheduler

## Synopsis
```queue subcommand ...```

## Description
`queue` maintains a prioritized list of jobs, submitted as independently
operating command lines.  Entries in the queue are processed in order by
one or more "queue runners" acting in parallel as background processes.
A per-user database of entries is maintained in the `~/.queue` directory.

## Subcommands

The following subcommands are defined:

`help`  
Displays help for all subcommands, plus additional background.

`help <subcommand>`  
Displays help for a given subcommand.

`add [<pri>]`  
Enqueues one or more jobs from standard input (one per line) and
launches a queue runner as a background process if none are running.
If a priority is specified, all commands are enqueued with the given
priority.  If no priority is specified, the default priority (none)
is assumed.  Valid priorities are lowercase letters 'a' through 'z'.

`remove [<id> ...]`  
Removes one or more jobs from the queue.

`move <srcid ...> <dstid>` (unimplemented)  
Moves one or more jobs to a new location in the queue by renumbering
the job ID or IDs.

`hold <id> ...`  
Marks unstarted jobs as unavailable for processing, without removing
them from the queue.  Internally, this changes the state of a job
from 'todo' to 'hold'.

`release <id> ...`  
Marks held jobs as available again for processing, without altering
their position in the queue, and launches new queue runners if
necessary.  Internally, this changes the state of a job from 'hold'
to 'todo'.

`retry <id> ...`  
Re-enqueues one or more failed or completed jobs for reprocessing.
Internally, this changes the state of a job from 'fail' (or 'done')
to 'todo' and removes any previously collected output.  Jobs
re-enqueued in this way retain their original ordinal number but
lose their original priority.

`priority <pri> <id>|<pri> ...`  
Assigns a different priority to one or more jobs.  The list of jobs can
be given by id or by existing priority.

`list`  
Lists all jobs in the queue.

`list todo|active|done|fail|hold`  
Lists all jobs in the specified state.

`list <id> ...`  
Lists all jobs matching the specified job ID or IDs.

`head [<limit>]`  
Lists the first few jobs in the queue.  (Default limit is 10.)

`tail [<limit>]`  
Lists the last few jobs in the queue.  (Default limit is 10.)

`status`  
Displays a summary of the current status, consisting of the
following information:  Total job count ("total"), number of
unstarted jobs remaining ("todo"), number of successfully completed
jobs ("done"), number of unsuccessfully completed jobs ("fail"),
number of active jobs ("active"), the number of held jobs ("hold"),
the current limit of queue runners ("limit"), and the run state of
the queue ("paused").

`pause`  
Enter "paused" state and cease dispatching new jobs.  Currently
active jobs continue execution to their natural completion.  Any
active queue runners are directed to exit as soon as their active
job completes.

`resume`  
Exit "paused" state and resume dispatching new jobs.  Launches one
or more queue runners in the background, if needed.

`limit`  
Displays the current maximum allowed number of simultaneous queue
runners for parallel processing of jobs.

`limit <limit>`  
Raises or lowers the maximum number of queue runners.  If the new
limit is higher than the current limit, new queue runners are
launched as background processes.  If the new limit is lower than
the current limit, one or more queue runners are directed to
complete their current job and exit at their earliest convenience.

`frontier`  
Displays the frontier of the queue, that is, a subset of jobs
consisting of those most recently completed, most soon to be
dispatched, and any currently executing jobs.

`watch`  
Enters a non-interactive queue-monitoring mode which displays the
queue's frontier and refreshes the display upon changes to the queue.
Monitoring mode is exited by pressing Ctrl-C.

`clean`  
Removes all completed jobs.  Does not remove unstarted jobs, active
jobs, or failed jobs.

`reset`  
Resets the job ID counter to zero.  A prerequisite for this is that
the queue must be empty.

`<id> ...`  
Displays the job file for the specified job ID number or numbers.
If the job is in the 'todo' or the 'active' state, the job file will
contain only the current working directory of the job and the job's
command line.  If the job is in the 'done' or 'fail' state, the job
file will also contain the collected standard output and standard
error streams from the job.

## Job IDs

Job IDs are given to certain subcommands as a single number, a numeric
range, or a list consisting of any arbitrary collection of single
numbers and numeric ranges.  Examples appear below.

    50            The single job ID 50.

    100-199       The 100 job IDs from 100 through 199, inclusive.

    1-3 5 9-99    The 95 job IDs 1, 2, 3, 5, and 9 through 99, inclusive.

## Job Formatting

When the queue's frontier is displayed, the following symbols appear at
the far left of the line, before the job's ID and command line:

    ▶   Active job
        Unstarted job
    ✓   Successfully completed job
    !   Unsuccessfully completed job
    ?   Held job

## Database

The following directories and files comprise a per-user database:

    ~/.queue            Main database directory.
    ~/.queue/.count     Counter representing the last-used job ID.
    ~/.queue/.limit     Upper limit on the number of parallel queue runners.
    ~/.queue/.lock      Internal lock file.
    ~/.queue/.mtime     Last modification time of queue.
    ~/.queue/todo       Directory containing unstarted jobs.
    ~/.queue/active     Directory containing actively processing jobs.
    ~/.queue/done       Directory containing successfully completed jobs.
    ~/.queue/fail       Directory containing unsuccessfully completed jobs.
    ~/.queue/hold       Directory containing held jobs.
