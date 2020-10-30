
Somewhat of a "fork" of: https://github.com/TerryHowe/ansible-modules-hashivault

We quite happily would just simply use upstream instead of maintaining this but 
unfortunately upstream has not yet migrated to collection and it seems impossible 
to have a standard (non-galaxy?) role repo as dependency for collection roles (in awx?). 
If there exists a solution it is quite hard to find and also rather unintuive as we more 
or less tried more or less anything before starting this.


This here still depends on upstream role deing installed and avaible to work!
The reason is that we found we dont need to add all upstream code into the 
collection, but "only" the module files is enough, while internal legacy 
code refs to module_utils still work directly with upstream code when the 
role is avaible. Unfortunately convincing ansible to correctly install the 
role is also a hard problem, so in summary we have to big problems when 
using legacy roles together with collections:


   1. Specifying the role in the right way so that the ansible system auto-installs it

        -> What works (for non-galaxy role using awx):

             -> the old standard way of adding the role to roles/requirement.yml in the (final) 
                project repo, this means that one must add all indirect role dependencies of 
                used collections explicitly to any project repo using these collections

        -> Unsuccesfully tested:

             -> roles/requirement.yml inside collection  ==>  seems to be simply ignored
             -> collections/requirement.yml inside collection  ==>  seems to be simply ignored
             -> roles/<role>/meta/main.yml inside collection  ==>  at least checks for role 
                  and raises an error when not found (so can be used at least as assertion), 
                  but does not install it

             -> adding role git-url to galaxy.yml dependency  ==>  does not work as system expects tar.gz at link (normal collection format), but maybe I simply dont know the correct format (??)


   2. Even when the role is properly installed, modules from it seems not to be resolvable 
      from inside collections roles

        -> What works (for non-galaxy role using awx):

             -> creating a wrapper collection and adding the modules there, this is obviously really bad in the long run as we copy and paste upstream code around

        -> Unsuccesfully tested:

             -> obviously just using the plain module name (like in the olden days)  ==>  name does not resolve
             -> auto-guessing a fqdn for modules  ==>  no variant I could come up with resolves the name

        -> Note that this might all be not so problematic if role is inside galaxy (which should guarantee a fqdn, right?)

