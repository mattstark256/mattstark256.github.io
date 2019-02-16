---
layout: post
title:  "Firelight"
date:   2019-02-16 00:00:00 +0100
tags: Unity
---
Firelight is a game I made as part of a team of 2 for Global Game Jam 2019. It's about searching for warmth in the cold dark night. The theme of the jam was "What home means to you".

[Link to the Global Game Jam submission][Global-Game-Jam]

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/LCdt8rgqWDE?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

We wanted the game to have cutscenes and a dialogue system where the player's choice of response could affect how the conversation progressed. We decided the cutscenes and conversations should be in scripts, because this would give us the flexibility and control to make each one unique. It allowed us to use conditional statements, for loops, variables etc. We used coroutines so we could pause execution while waiting for the player's response or waiting for an animation to finish.

Here is an example of a section where the camera focuses on an NPC who then asks the player a question.

    // Smoothly move the camera to the NPC and wait until that is complete
    cutscene.Transition(transform.position);
    while (cutscene.IsTransitionInProgress()) { yield return null; }
        
    // Show a speech bubble with two dialogue options and wait until the player has chosen one
    cutscene.DialogueDecision("Are you the rescue team? I called to be rescued.", "I'm afraid not.", "Yes... I'm part of the rescue team...", true);
    while (cutscene.IsDialogueInProgress()) { yield return null; }

    if (cutscene.GetDecisionOutcome())
    {
        // This is where the response to the first option would be
    }
    else
    {
        // This is where the response to the second option would be
    }

[Global-Game-Jam]: https://globalgamejam.org/2019/games/firelight