CS3214 Cush Shell

Student Information
-------------------
- Ishita Gupta
- Jayant Dulani

How to Execute the Shell
------------------------
To run the shell:
    ./cush

Then, enter commands at the shell prompt — they should execute as expected.

To run the tests:

Custom tests:
    /home/ugrads/majors/ishitagupta/CS3214/project-1/tests/stdriver.py -o output_spec.py -v custom_tests.tst

Basic and advanced tests:
    /home/ugrads/majors/ishitagupta/CS3214/project-1/tests/stdriver.py -o output_spec.py -b -a

Important Notes
---------------
- Basic functionality implemented: cd command
- Advanced functionality implemented: history command

Description of Base Functionality
---------------------------------

jobs:
- Implemented in jobs_command() (in cush.c)
- Iterates over job_list and uses print_job() to display job ID, status, and command line.

fg:
- Brings a background or stopped job to the foreground.
- Implemented in fg_command(int job_id)
- Retrieves job with get_job_from_jid(job_id), sets terminal control using termstate_give_terminal_to(), sends SIGCONT, and waits with wait_for_job().

bg:
- Resumes a stopped job in the background.
- Implemented in bg_command(int job_id)
- Sets status to BACKGROUND and sends SIGCONT.

kill:
- Sends SIGKILL to terminate a job.
- Implemented in kill_command(int job_id)
- Sends signal to process group using kill(-job->gpid, SIGKILL).

stop:
- Sends SIGTSTP to stop a job.
- Implemented in stop_command(int job_id)
- Updates status to STOPPED.

^C (Ctrl+C):
- Shell ignores SIGINT with signal_set_handler(SIGINT, SIG_IGN).
- Foreground jobs still receive SIGINT.

^Z (Ctrl+Z):
- Handles SIGTSTP to stop foreground jobs.
- Marks the job as STOPPED and saves terminal state.

Description of Extended Functionality
-------------------------------------
I/O Redirection:
- Uses posix_spawn_file_actions_addopen() and posix_spawn_file_actions_adddup2().
- Input (<): Opens file and redirects to STDIN_FILENO.
- Output (> and >>): Opens or appends to file and redirects to STDOUT_FILENO.

Pipes:
- Created using pipe() system call.
- Duplicates file descriptors using posix_spawn_file_actions_adddup2().
- Handles multiple piped commands.

Exclusive Access:
- Only the foreground job has terminal control using tcsetpgrp().
- Terminal state saved/restored using termstate_save() and termstate_give_terminal_back_to_shell().

Additional Built-in Commands
----------------------------

cd:
- Changes the current working directory.
- Implemented in cd_command(struct ast_command *cmd)
- Supports:
    - cd: Goes to the home directory.
    - cd [directory]: Goes to specified path.

history:
- Shows list of past commands from the session.
- Implemented in history_command(struct ast_command *cmd)
- Uses readline library functions: history_get_history_state() and history_list().

exit:
- Exits the shell cleanly.
- Implemented in exit_command()
- Sends SIGKILL to all jobs before exiting.
