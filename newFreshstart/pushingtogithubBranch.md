
PS C:\Users\anand\Downloads\webtrak> git branch feature/comp_off
PS C:\Users\anand\Downloads\webtrak> git checkout feature/comp_off
M       src/main/java/com/webknot/webtrak/controller/UserRequestServiceController.java
M       src/main/java/com/webknot/webtrak/dto/UserBasicDto.java
A       src/main/java/com/webknot/webtrak/dto/UserSummaryDTO.java
M       src/main/java/com/webknot/webtrak/repository/AllocationsRepository.java
M       src/main/java/com/webknot/webtrak/service/UserRequestService.java
M       src/main/java/com/webknot/webtrak/service/UserService.java
Switched to branch 'feature/comp_off'
PS C:\Users\anand\Downloads\webtrak> git add .
warning: in the working copy of 'src/main/java/com/webknot/webtrak/dto/UserBasicDto.java', CRLF will be replaced by LF the next time Git touches it
warning: in the working copy of 'src/main/java/com/webknot/webtrak/dto/UserSummaryDTO.java', CRLF will be replaced by LF the next time Git touches it
warning: in the working copy of 'src/main/java/com/webknot/webtrak/repository/AllocationsRepository.java', CRLF will be replaced by LF the next time Git touches it
PS C:\Users\anand\Downloads\webtrak> git commit -m "feat(comp_off):added the get metod for showing all the managers"
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'anand@webknot.(none)')
PS C:\Users\anand\Downloads\webtrak> git user.name "abhisheka-rgb"
git: 'user.name' is not a git command. See 'git --help'.
PS C:\Users\anand\Downloads\webtrak> git config  user.name "abhisheka-rgb"
PS C:\Users\anand\Downloads\webtrak> git config  user.email "abhishek.a@webknot.in"
PS C:\Users\anand\Downloads\webtrak> git push 
fatal: The current branch feature/comp_off has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin feature/comp_off

To have this happen automatically for branches without a tracking
upstream, see 'push.autoSetupRemote' in 'git help config'.

PS C:\Users\anand\Downloads\webtrak> git push --set-upstream origin feature/comp_off
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: 
remote: Create a pull request for 'feature/comp_off' on GitHub by visiting:
remote:      https://github.com/gowebknot/webtrak/pull/new/feature/comp_off
remote:
To github.com:gowebknot/webtrak.git
 * [new branch]      feature/comp_off -> feature/comp_off
branch 'feature/comp_off' set up to track 'origin/feature/comp_off'.
PS C:\Users\anand\Downloads\webtrak> git add .
PS C:\Users\anand\Downloads\webtrak>     