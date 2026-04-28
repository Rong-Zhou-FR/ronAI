# Common sense Git

## Commit 

### WHEN: end of phase

- Function unit implemented 
- Project restructured
- Test ran and changes made on a particular module

### WHEN NOT:

- Just to save work

### HOW: customary commit message



## Branch: ensuring safe roll back point

- Risky operations should be NEVER be on `main`
  - Major refactor  
  - Dependency upgrade  
  - Merging branches  
  - Rewriting history  

```bash
git switch -c {branch-name} # -c creates new branch
git config push.autoSetupRemote True # set git to autocreate remote branch if not present
git push # create branch in remote
```
