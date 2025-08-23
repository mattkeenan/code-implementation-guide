# scratchpad.md udpate command

## scratchpad.md file

the `scratchpad.md` file (or just `scratchpad`) is used to track the immediate next task(s). it is used to keep a plan of the what's next, how to implement the next task(s), and typically reviewed before implementing the plan unless specifically told otherwise.

the `scratchpad.md` file is kept in the git repo root or the project root directory. the scratchpad should **not** be staged or committed into the repo (the file name should be in the .gitignore file unless specifically told otherwise).

if asked to update the file and the file doesn't exist then create the file.

## update command

**CRITICAL** it is important to do these step in order so that we minimise loss of information in the case of a context roll over (i.e. all context used up in a conversation part way through the process)

**CRITICAL** do **not** delete details of how to implement the next task(s) from the scratchpad.

1. completed tasks
   1.1. old completed tasks in the scratchpad should be summarised and put in the implementation guide as a completed item
        1.1.1 **CRITICAL** make sure to follow the rules on how to update the implementation guide; check the project .claude/commands directory to find the necessary commands
   1.2. then completed items should be removed from the scratchpad.

2. update the `scratchpad` with a brief implementation plan on the next task for review.

3. and lastly remove any items that are **irrelevant** for the task(s) in the scratchpad.

## post-scratchpad update

always put an entry at the top of the scratchpad to not start implementing anything until the plan has been reviewed by the user
