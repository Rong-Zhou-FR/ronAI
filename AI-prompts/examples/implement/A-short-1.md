- 1. Revert. 2. Your opinions should instead be added as comments 3. keep issues open, we need further discussion.

- issue 2,3 has received approval from our consultant. update issues. implement changes as appropriate in a separate branch and create pull request

- local file paths changed. Bring yourself up to speed. We are still at ~/kodo/autish, but the old autish files now live in ./autish-legacy subfolder, and all the A-* modules are also subfolders of .

- Let's focus on issue #11. Compare PEP 621 and poetry. Also, if we take the opportunity to move to uv, what would that look like ? 

- Now focus on #11. What did the external consultant (Rong-Zhou-FR) say ?

- So what would A core handle and not handle in this case ?

- /eval I value architect's professional knowledge. But I am siding CRUDService. Later rewriting can be very difficult if every module already went their own way. We can anticipate the needed CRUDServices from Autish-legacy, since all A modules currently being developped are direct descendents of Autish. By reading autish code/docs, we already have a general idea of needed CRUD services.

- Continue. Consult architect and make a plan first. Open github issues on relevant repo to track progress.

- Why the A module repos are created as private repos ? They should all be public ! "write_memory", by default, all repo at our organization are public, as we are really, really militantly FOSS

- issue's target schema is wrong. A-encik is a rewrite of Autish encik and must have the same functionalities and userspace. See ./autish-legacy for reference.

- merge branch into main. If there are other side branches in A-core, merge them all in in correct order into main, then delete them.

- Now #6 update Github issue with your proposal
