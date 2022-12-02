# How to do a code review

A long long long time ago in a big corporate IT department sat a junior dev, just started. Ahhh, I remember my first code review well. Prior to my first code review, I had been working for 2 quite small companies where there were a maximum of 5 developers, and, even though I was inexperienced I was considered an expert in the areas I was working in. Nobody had ever formally reviewed my code. Now, I was just a tiny dev resource in a large travel company working in an IT department that covered the whole floor of a large office building. It also had one of those proper server rooms with grey boxes, disk drives the size of fridges and a fire system that I was told could kill you if you didnt exit the server room in under 10 seconds.

The program was a report, with headings and sub headings and sub totals and all that and eventually it would be coded in VAX COBOL. But, I had been asked to write it in pseudo code on paper first. This was written on preprinted form which had sections such as ACTION: and EXPLANATION: . The theory was that anything could be expressed as either a loop (while), and if statement (when) or an assignment (assign). 

Once finished 4 copies of each page were made and popped into a distribution envelop for the reviewers, and a meeting was scheduled for several days time. The review was held n a meeting room on the third floor of the office building, my first time on the third floor. In the meeting sat my team leader and mentor, along with another programmer, a business analyst, and a senior dev from another team.

They read each page and commented on its contents. One reviewer wanted the variable names to have some strange prefix convention, and then another complained that a loop counter name was too long. I dont remember the details now, but not a single page was left without a correction. After 30 minutes of grilling I left the room almost in tears. But I did learn something. How not to do a code review.

Things have moved on. I never expect to have to do a code review of a handwritten program. Now its all Pull Requests and online comments.

The aim of the code review is to maintain the quality of the code base to a certain standard, but also to mentor and educate the team.
A code review should only stall the process if there is a serious fault that would introduce a bug. Everything else is just opinion and can be dealt with according to the priority at the time. Getting that element right is tricky. For example: A hot fix going into production does have to be code reviewed, and I welcome that, and require it. I am happy for another dev to take a look at my fix, confirm I am not doing anything stupid. But I do not want the dev to block the change just because I used an underscore in a variable name and thats not in the standards. The priority in this instance is to fix production with the least stress possible.

If however, we are in the normal SDLC and a junior dev has submitted a change that ignores the code standards completely, then that is possibly a justifiable blocker on the PR.

#### Use the MOSCOW model
Each comment should be prefixed with one of the following MUST:, SHOULD:, COULD:, or WOULD:
And each comment should be accompanied by en explanation and if possible an effective solution.

- MUST: The code has to change before approval. The need to change is not open for discussion, but the solution can be.
- SHOULD: Suggestions for improvement. This is open for discussion. This is an opinion and should not block the PR. I would still expect the submitter to provide an explanation or reasoning, if no change was made following the comment.
- COULD: This is for suggestions of ways to make the code better. These can be ignored without giving a reason. (But they are educational).
- WOULD: The reviewer would have done this a different way. This will relate to a whole bunch of code or solution and is used as a medium for mentoring and education.





