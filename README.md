# Berops Culture

## Table of Contents
- [Tools](#tools)
- [2FA](#2fa)
- [Git](#git)
- [Development in Go](#golang)
- [Communication](#communication)
- [Your Friday](#yourfriday)
- [Remote Kanban DevOps house](#remotekanban)
- [Interviews](#interviews)
- [Equipment](#equipment)

<a name="tools"/>

## Tools

We use:
- [Slack](https://www.slack.com) for chat communication; incl. hooks from other tools (e.g. GitHub) and bots (e.g. [GitHub Release Notifier](https://github.com/justwatchcom/github-releases-notifier))
- [GitHub](https://www.github.com/berops) for private and public git repositories
- [GSuite](https://gsuite.google.com) for mailbox, cloud storage and other office tools
- [Google Cloud Platform](https://cloud.google.com) for hosting our infrastructure
- [Kimai](https://www.kimai.cloud/) for recording worked hours. It isn't a real-time tracking tool, but rather it is used to log hours upon task completion, ensuring privacy.


<a name="2fa"/>

### 2-Factor Authentication

We enforce 2FA where possible. If a tool doesn't support 2FA, we don't feel comfortable using it. \
**Please make sure you enable 2FA on all the accounts.**

<a name="git">

## Git

All our work is stored on GitHub in private and public repositories. There are common housekeeping rules that apply to both of the repository types.
- Anybody can open a PR
- `main` and `master` branches are considered protected and the only commits in these branches should be via the Pull Requests
- All the work is done in `feature/` branches
- If a person wants to get his/her `feature/` branch merged into the `main` branch, he/she opens a PR and may or may not assign it to a person
- PRs get merged after a review; review needs to be done by someone else than the person who logged the PR
- Anybody can review and merge anybody else's PR (except of his/her own one)
- We care about the commit and PR [hygiene](https://chris.beams.io/posts/git-commit/)
- PRs should be merged via the [squash and merge](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges#squash-and-merge-your-pull-request-commits) button


<a name="golang">

## Development in Go

Our major product, [Claudie](https://github.com/Berops/claudie), is developed in [Go](https://go.dev/).
We like the following guidelines:
- [Uber Go](https://github.com/uber-go/guide)
- [Dave Cheney - Practical Go](https://dave.cheney.net/practical-go/presentations/gophercon-israel.html)

<a name="communication">

## Communication

Our communication "defaults to open". This means that by default every information is open to the colleagues (strategic exceptions are expected and will be carefully assessed). The reasons:
1. Empowering colleagues - we're all a part of the same business story, we're working together to achieve a success together
2. Quality decisions - in a transparent world we're expecting that we get a constructive feedback and we're able to steer the decisions accordingly
As Slack is our communication tool of choice, we take the advantage of `huddle`s. Anybody is welcome to join any huddle.

<a name="yourfriday">

## Your Friday

We believe that it's the field engineers, who deliver the work for our clients, who have the best sense of the market needs. In order to utilize this potential, we encourage our colleagues to use 20% of their working hours to work on things they choose. There's just one requirement: these topics need to be aligned with the company's mission. The activities which are typically done within _Your Friday_ program are:
- Learning about new tech
- Running experiments
- Building prototypes
- Training and certifications (fully reimbursed)
- Writing blogs, running webinars and working with the community
- Contributing to open-source projects

The cost of this program is high, so you might wonder why are we doing this. These are the key reasons:
1. Innovation potential
2. Having fun
3. Boosting our skill set

<a name="remotekanban">

## Remote Kanban DevOps house

Berops is a full-remote, Kanban-style DevOps workplace. This section contains an additional list of good practices to help you succeed in such an environment.

- Default to persisting information in the form of text on agreed upon channels, with open visibility to all potentially relevant people (Slack, GitHub...)
  - Text is permanent, so it can be revisited by anyone at any later point.
  - Text subconsciously forces you to specify and develop the idea as you're recording it in writing.
  - Text is inclusive to people that are not currently present.
  - Text is an asynchronous form of communication, so the other party can participate when it suits them better.
- Use face-to-face communication for situations when text-based communication is too cumbersome.
  - But ensure that all worthwhile results of a face-to-face communication are also recorded in text form.
    - For example, if you have a design meeting where you agree upon the approximate architecture of your product, take the effort to record it in writing.
- Work in pair via video call screen sharing from time to time.
  - It's good for sharing knowledge & skills, building relationships, and finding alternative solutions.
- Have all your work reviewed by your colleagues. Don't have everything reviewed by the same person, but have a review request rotation including your entire team.
  - It's useful for sharing knowledge & skills, familiarizing at least one more person with your work, and improving the quality of the work done.
- Take deep ownership of the tasks you're assigned to.
  - Cooperate with the relevant people, if necessary.
  - Consider all consequences and prepare for them.
  - It's the assignee's responsibility that the task is completed in a way that serves its intended purpose - nothing more, nothing less.
- Contribute towards making the kanban board reflect the project's goals and priorities as accurately as possible.
  - If you think that there is something that needs doing, open a ticket for it.
  - Each task must have a clear goal and purpose.
  - Each task must be described well enough so that any team member could pick it up and work on it - even a fresh hire 6 months later.
  - Try to submit regular updates to the tasks you're actively working on, generally at least one update per workday.
  - If you think some task doesn't make sense doing anymore, or isn't as important anymore, let it be known.

<a name="interviews">

## Interviews

Interviews are difficult for both - the interviewer and the interviewee. There is a good reason why they are difficult. It is because they are important. We prefer to have three Berops people present on the interviews in order to capture the impressions about the candidate. This way we are able to spot aspects of the candidate's profile that a single person might miss due to the extra load that the interviewer needs to handle.
Anybody is welcome to join any interview until all three seats are taken. All interviews are announced in the Slack `#hiring` channel.

The interview process at Berops is relatively standard, without any unusual hassle to worry about. From our side, we are interested in the following aspects of the candidate:
- understanding of what our particular line of work is generally all about (DevOps, Kubernetes, consulting)
- understanding that it might be necessary to learn a lot in the first several months on the job
- drive and motivation to do what we do
- approximate technical skill level, particularly in ways relevant to our work
- past professional experiences and their relevance
- culture fit within the team
- identifying the candidate’s expectations and requirements

The goal of each candidate’s interview process is to try to find ways to make both parties professionally happy. We communicate openly and encourage the candidate to do the same. Structure of the typical interview is simple.

Introduce yourself to us, preferably by sending us an email that contains your CV
1. We might get in touch with you (call or email) for a basic clarification that would help us with screening the CV
2. Judging by the CV, if you seem to be a good fit, we will schedule a call with you
3. The meeting is done online via a video call. The meeting is loosely structured as follows:
    - Introduction of Berops
    - Job description, discussion about technical background required for the job and deeper candidate assessment; together with a short quiz designed to sample the candidate’s general knowledge of mostly job-related topics
    - Candidate’s expectations
    - Open discussion

The candidate receives feedback usually within a week of the meeting. In most cases, this concludes the interview process.

<a name="equipment">

## Equipment

Berops provides working equipment for the employees. This typically covers a working station, a table and a chair.

The working station is typically:
- Laptop from T14/T15 Lenovo series or MacBook Air/Pro with sufficient memory
- External dual-screen setup
- Docking station and peripherals
- Headset (we have good experience with Koss, Jabra, Anker and Sony over-the-ear comfy headphones)
- Backpack (we default to Thule)

The above listed are our defaults. Here we are flexible and if you have different and reasonable preferences, we'll accept them (e.g. different specs, brands, ...).
If the person is located within an EU country, the company procures the working station hardware and delivers it to the person's address. Otherwise the provisioning will be done individually by the colleagues based on the specifics of the country where they are located.

We offer a EUR 500.- budget for a working desk and EUR 500.- budget for a chair.

This program is optional and people who prefer to use their own hardware or other equipment are most welcome to do so.

### What happens to the equipment if I leave the company?

At Berops here we have a policy for handling the equipment in case of resignations.
- If you work from EU, you send the working station to our office address, the delivery expenses are on the company's bill.
- If you work from non-EU country, you will keep the working station, and you'll need to compensate the remaining value of the equipment.
- In the cases of chairs and tables (no matter where you're located), you'll need to compensate the remaining value of the equipment.

#### The remaining value of the equipment

The following scheme applies:
- If you resign effectively on day 1-182, you can keep the item for 60% of the purchasing price
- If you resign effectively on day 183-365, you can keep the item for 45% of the purchasing price
- If you resign effectively on day 366-547, you can keep the item for 30% of the purchasing price
- If you resign effectively on day 548-730, you can keep the item for 15% of the purchasing price

If you resign after 2 years of ownership of the specific item, you can just keep it.


Our culture and this content are subject to further development.
