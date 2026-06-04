Now, create the given repositories 

- A-vorto, based on Autish vorto
- A-encik, based on Autish encik
- A-organizi, based on Autish kalendaro, todo, taglibro
- A-lien, based on Autish retposto, kontakto


For each, set up the AGENTS.md and README.md. Emphasize that they are dependent modules on A-core, and they must use A-core common utilities whenever possible. If the module needs something that is of interest to other A modules and should have been part of core, or an enhanced version of a core function, one must create a Github issue on core instead of writing the function themselves.

Also, make it clear that those modules are historically dependent on each other in Autish for certain functionalities. The correct way to handle those is to detect at call time if the depended module is installed. If not, escalate error to userspace and offer to install said module automatically.

Push them to Github via gh.
