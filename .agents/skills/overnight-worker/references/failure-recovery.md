# Failure recovery

The overnight runner, not the agent, is responsible for:
- timeout enforcement
- saving logs
- saving patches
- resetting to the start commit after failures or timeouts
- continuing to the next task

The agent should help this process by:
- keeping changes small
- stopping when blocked instead of thrashing
- leaving clear summary or blocker output
- avoiding destructive git commands unless explicitly instructed by the runner

When a task starts failing repeatedly:
1. verify the baseline
2. try the narrowest fix
3. re-run the smallest meaningful check
4. after 3 cycles with the same class of failure, stop and write a blocker
