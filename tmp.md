```shell
# Currently we use e67775a312f1f3ad32137423fe2b311a397f5293 commit of Docsy, using a Git submodule.
# In order to not change the version part of the migration to Hugo Modules, we use the same commit.

# Switch to website folder
❯ cd website
❯ pwd
/Users/aliok/go/src/github.com/cncf/tag-contributor-strategy/website

# Hugo cannot get e67775a312f1f3ad32137423fe2b311a397f5293 directly as Docsy was not a Go module at that time.
# Use v0.6.0 instead and then switch to e67775a312f1f3ad32137423fe2b311a397f5293
❯ hugo mod get github.com/google/docsy@v0.6.0
go: downloading github.com/google/docsy v0.6.0
go: added github.com/google/docsy v0.6.0

❯ go get github.com/google/docsy@e67775a312f1f3ad32137423fe2b311a397f5293
go: downloading github.com/google/docsy v0.0.0-20201102101051-e67775a312f1
go: downgraded github.com/google/docsy v0.6.0 => v0.0.0-20201102101051-e67775a312f1

# Docsy itself is at the version we would like. However, the dependencies are not.
# Docsy needs https://github.com/FortAwesome/Font-Awesome.git and https://github.com/twbs/bootstrap.git, but
# since version e67775a312f1f3ad32137423fe2b311a397f5293 doesn't use Go modules, the dependencies are not included.
# Version v0.6.0 of Docsy uses github.com/google/docsy/dependencies@v0.6.0 but Font-Awesome and Bootstrap coming
# with that dependency module are not the same as the ones used by Docsy at version e67775a312f1f3ad32137423fe2b311a397f5293.
# So we need to replace the dependencies with the ones used by Docsy at version e67775a312f1f3ad32137423fe2b311a397f5293.

# Remove the dependencies module from dependencies
❯ go get github.com/google/docsy/dependencies@none
go: removed github.com/google/docsy/dependencies v0.6.0

# Add the dependencies module from e67775a312f1f3ad32137423fe2b311a397f5293
# See https://github.com/google/docsy/tree/e67775a312f1f3ad32137423fe2b311a397f5293/assets/vendor for their commits
❯ go get github.com/FortAwesome/Font-Awesome@951a0d011f8c832991750c16136f8e260efa60b5
go: downloading github.com/FortAwesome/Font-Awesome v0.0.0-20200715213439-951a0d011f8c
go: downgraded github.com/FortAwesome/Font-Awesome v0.0.0-20220831210243-d3a7818c253f => v0.0.0-20200715213439-951a0d011f8c

❯ go get github.com/twbs/bootstrap@a716fb03f965dc0846df479e14388b1b4b93d7ce
go: downloading github.com/twbs/bootstrap v4.5.3+incompatible
go: downgraded github.com/twbs/bootstrap v4.6.2+incompatible => v4.5.3+incompatible

# Remove the `theme` entry in Hugo config, as we're switching over to Hugo Modules
❯ sed -i '/# Hugo allows theme composition (and inheritance). The precedence is from left to right./d' config.toml
❯ sed -i '/theme = \["docsy"\]/d' config.toml

# Add the Hugo Modules config, under the `module` entry in Hugo config
# TODO: NOTE: once we switch to new versions of Docsy, we should not directly depend on Font-Awesome and 
# Bootstrap, but use Docsy's dependencies module instead. 
# manual work... Multiline sed is hard!
[module]
  proxy = "direct"
  # TODO: following are going to be enabled once we switch to new versions of Docsy and Hugo
  # [module.hugoVersion]
  #  extended = true
  #  min = "0.73.0"
  [[module.imports]]
    path = "github.com/google/docsy"
  # TODO: once we switch to a newer version of Docsy, do not directly depend on Font-Awesome and Bootstrap, 
  #       but use Docsy's dependencies module instead
  [[module.imports]]
    path = "github.com/FortAwesome/Font-Awesome"
  [[module.imports]]
    path = "github.com/twbs/bootstrap"
EOL

# Check mod graph
❯ hugo mod graph
hugo: downloading modules …
hugo: collected modules in 25431 ms
github.com/cncf/sig-contributor-strategy/website github.com/google/docsy@v0.0.0-20201102101051-e67775a312f1
github.com/cncf/sig-contributor-strategy/website github.com/FortAwesome/Font-Awesome@v0.0.0-20200715213439-951a0d011f8c
github.com/cncf/sig-contributor-strategy/website github.com/twbs/bootstrap@v4.5.3+incompatible

# Remove Docsy Git submodule
❯ git rm -rf themes/docsy
rm 'website/themes/docsy'

# Test the build
❯ cd ..
❯ pwd /Users/aliok/go/src/github.com/cncf/tag-contributor-strategy
❯ mage build
hugo: collected modules in 27811 ms
INFO 2023/05/12 20:29:53 Using config file: 
Start building sites … 
INFO 2023/05/12 20:29:53 syncing static files to /src/website/public/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/required/contributing redirecting to /maintainers/templates/contributing/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/required/governance-elections redirecting to /maintainers/templates/governance-elections/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/required/governance-intro redirecting to /maintainers/templates/governance-intro/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/required/governance-maintainer redirecting to /maintainers/templates/governance-maintainer/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/required/governance-subprojects redirecting to /maintainers/templates/governance-subprojects/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/issue-labels redirecting to /maintainers/templates/issue-labels/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/required/maintainers redirecting to /maintainers/templates/maintainers/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates/recommended/reviewing redirecting to /maintainers/templates/reviewing/
DEBUG 2023/05/12 20:29:55 creating alias: /maintainers/github/templates redirecting to /maintainers/templates/
DEBUG 2023/05/12 20:29:55 Render page Search Results to "/search/index.html"
DEBUG 2023/05/12 20:29:55 Render page Contributing Guide to "/about/contributing/index.html"
DEBUG 2023/05/12 20:29:55 Render page CNCF TAG Contributor Strategy Charter to "/about/charter/index.html"
DEBUG 2023/05/12 20:29:55 Render page Governance Working Group to "/about/governance/index.html"
DEBUG 2023/05/12 20:29:55 Render page Maintainers Circle Working Group to "/about/maintainers-circle/index.html"
DEBUG 2023/05/12 20:29:55 Render page Contributor Growth Working Group to "/about/contributor-growth/index.html"
DEBUG 2023/05/12 20:29:55 Render page Mentoring Working Group to "/about/mentoring/index.html"
DEBUG 2023/05/12 20:29:55 Render page Do you want to start contributing to open source and need help figuring out where to begin? to "/contributors/getting-started/index.html"
DEBUG 2023/05/12 20:29:55 Render page CNCF Projects to "/contributors/projects/index.html"
DEBUG 2023/05/12 20:29:55 Render page Community CRM Runbook to "/maintainers/community/crm-runbook/index.html"
DEBUG 2023/05/12 20:29:55 Render page Project Health Measurement to "/maintainers/community/project-health/index.html"
DEBUG 2023/05/12 20:29:55 Render page Keeping contributors engaged after their first contribution to "/maintainers/community/contributor-growth-framework/engagement/index.html"
DEBUG 2023/05/12 20:29:55 Render page Incentivizing contributors to move up the ladder to "/maintainers/community/contributor-growth-framework/incentivizing-contributors/index.html"
DEBUG 2023/05/12 20:29:55 Render page Incentivizing non-code contributions to "/maintainers/community/contributor-growth-framework/incentivizing-non-code/index.html"
DEBUG 2023/05/12 20:29:55 Render page Long-term contributors to "/maintainers/community/contributor-growth-framework/long-term-contributors/index.html"
DEBUG 2023/05/12 20:29:55 Render page Motivating users to contribute to "/maintainers/community/contributor-growth-framework/motivation/index.html"
DEBUG 2023/05/12 20:29:55 Render page A successful PR workflow to "/maintainers/community/contributor-growth-framework/pr-workflow/index.html"
DEBUG 2023/05/12 20:29:55 Render page Charter: Mission, Scope, Values, and Principles to "/maintainers/governance/charter/index.html"
DEBUG 2023/05/12 20:29:55 Render page Governance: Leadership Selection to "/maintainers/governance/leadership-selection/index.html"
DEBUG 2023/05/12 20:29:55 Render page Overview to "/maintainers/governance/overview/index.html"
DEBUG 2023/05/12 20:29:55 Render page Security Guidelines for New Projects to "/maintainers/security/security-guidelines/index.html"
DEBUG 2023/05/12 20:29:55 Render page HowTo: Make a Contributing Guide to "/maintainers/templates/contributing/index.html"
DEBUG 2023/05/12 20:29:55 Render page The Steering Committee Elections Template to "/maintainers/templates/governance-elections/index.html"
DEBUG 2023/05/12 20:29:55 Render page Using the Governance Templates to "/maintainers/templates/governance-intro/index.html"
DEBUG 2023/05/12 20:29:55 Render page The Maintainer Council Template to "/maintainers/templates/governance-maintainer/index.html"
DEBUG 2023/05/12 20:29:55 Render page The Governance By Subprojects Template to "/maintainers/templates/governance-subprojects/index.html"
DEBUG 2023/05/12 20:29:55 Render page Issue Labels for New Contributors to "/maintainers/templates/issue-labels/index.html"
DEBUG 2023/05/12 20:29:55 Render page Maintainer List Template to "/maintainers/templates/maintainers/index.html"
DEBUG 2023/05/12 20:29:55 Render page HowTo: Make a Reviewing Guide to "/maintainers/templates/reviewing/index.html"
DEBUG 2023/05/12 20:29:55 Render page Templates to "/resources/templates/index.html"
DEBUG 2023/05/12 20:29:55 Render page Collaborative Leadership: Governance Beyond Company Affiliation to "/resources/videos/2020/collaborative-leadership/index.html"
DEBUG 2023/05/12 20:29:55 Render page CNCF Project Paperwork Working Session to "/resources/videos/2020/project-paperwork/index.html"
DEBUG 2023/05/12 20:29:55 Render page Build Your Contributor Pipeline to "/resources/videos/2021/build-your-contributor-pipeline/index.html"
DEBUG 2023/05/12 20:29:55 Render page From Storming to Performing: Growing Your Project's Contributor Experience to "/resources/videos/2021/growing-your-projects-contributor-experience/index.html"
DEBUG 2023/05/12 20:29:55 Render page The Maintainer's Toolkit: Must-know CNCF Resources for Project Owners to "/resources/videos/2021/maintainer-toolkit/index.html"
DEBUG 2023/05/12 20:29:55 Render page Measuring the Health of Your CNCF Project: Going Beyond Stars and Forks to "/resources/videos/2021/measure-project-health/index.html"
DEBUG 2023/05/12 20:29:55 Render page Turn Contributors into Maintainers with TAG Contributor Strategy to "/resources/videos/2021/turn-contributors-into-maintainers/index.html"
DEBUG 2023/05/12 20:29:55 Render page The Future of Open Source Contributions from KubeCon Europe to "/resources/videos/2022/future-of-oss-contributions/index.html"
DEBUG 2023/05/12 20:29:56 Render page Good Governance Practices for CNCF Projects to "/resources/videos/2022/good-governance-practices/index.html"
DEBUG 2023/05/12 20:29:56 Render page TAG Contributor Strategy Livesteam to "/resources/videos/2022/livestream/index.html"
DEBUG 2023/05/12 20:29:56 Render page Nurturing the Whole Project to "/resources/videos/2022/nurture-the-whole-project/index.html"
DEBUG 2023/05/12 20:29:56 Render page Helping Open-Source Projects Level Up to "/resources/videos/2023/helping-open-source-projects-level-up/index.html"
DEBUG 2023/05/12 20:29:56 Render page Working Groups to "/about/working-groups/index.html"
DEBUG 2023/05/12 20:29:56 Render page Contribute to the CNCF Ecosystem to "/contributors/index.html"
DEBUG 2023/05/12 20:29:56 Render page Project Guidance to "/maintainers/index.html"
DEBUG 2023/05/12 20:29:56 Render page About TAG Contributor Strategy to "/about/index.html"
DEBUG 2023/05/12 20:29:56 Render page Community to "/maintainers/community/index.html"
DEBUG 2023/05/12 20:29:56 Render page Contributor Growth to "/maintainers/community/contributor-growth-framework/index.html"
DEBUG 2023/05/12 20:29:56 Render page Governance to "/maintainers/governance/index.html"
DEBUG 2023/05/12 20:29:56 Render page Security to "/maintainers/security/index.html"
DEBUG 2023/05/12 20:29:56 Render page CNCF Contributors to "/index.html"
DEBUG 2023/05/12 20:29:56 Render page Resources to "/resources/index.html"
DEBUG 2023/05/12 20:29:56 Render page Templates to "/maintainers/templates/index.html"
DEBUG 2023/05/12 20:29:56 Render page TAG Contributor Strategy Videos to "/resources/videos/index.html"
DEBUG 2023/05/12 20:29:56 creating alias: /resources/videos/page/1/index.html redirecting to /resources/videos/
DEBUG 2023/05/12 20:29:56 Render TAG Contributor Strategy Videos to "/resources/videos/page/2/index.html"
DEBUG 2023/05/12 20:29:56 Render XML for "sitemap" to "/sitemap.xml"
DEBUG 2023/05/12 20:29:56 Render 404 page to "/404.html"
DEBUG 2023/05/12 20:29:56 Render page CNCF Contributors to "/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Project Guidance to "/maintainers/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Community to "/maintainers/community/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Contribute to the CNCF Ecosystem to "/contributors/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Working Groups to "/about/working-groups/index.xml"
DEBUG 2023/05/12 20:29:56 Render page About TAG Contributor Strategy to "/about/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Contributor Growth to "/maintainers/community/contributor-growth-framework/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Governance to "/maintainers/governance/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Security to "/maintainers/security/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Templates to "/maintainers/templates/index.xml"
DEBUG 2023/05/12 20:29:56 Render page Resources to "/resources/index.xml"
DEBUG 2023/05/12 20:29:56 Render page TAG Contributor Strategy Videos to "/resources/videos/index.xml"
DEBUG 2023/05/12 20:29:56 Render Robots Txt to "robots.txt"
Error: Error building site: TOCSS: failed to transform "scss/main.scss" (text/x-scss): SCSS processing failed: file "stdin", line 6, col 1: File to import not found or unreadable: ../vendor/bootstrap/scss/bootstrap. 
Total in 30407 ms
Error: running "docker run --rm -v /Users/aliok/go/src/github.com/cncf/tag-contributor-strategy:/src sig-contributor-strategy --debug --verbose" failed with exit code 255


```
