---
name: Project lessons
description: Find and apply lessons from past mistakes in docs/ai/lessons before starting a ticket. Load at the start of every ticket.
---
# Project lessons

Lessons are short notes about mistakes that models have made in this repo, and how to avoid them.

1. Open `docs/ai/lessons/README.md` and read the index table.
2. Load only:
   - the lessons whose IDs the ticket names under "Skills and lessons to load", and
   - lessons whose `topic` matches the ticket's area, or whose `applies_to` glob matches a file in scope.
3. Read each lesson's **Wrong** and **Right** sections. Follow **Right**.
4. Do not load every lesson. That wastes context.

If you make a mistake that a lesson already describes, say so in the PR ("hit lesson 004 again"). The tutor then bumps `seen`. When `seen` reaches 2, the lesson gets promoted into a structural fix.
