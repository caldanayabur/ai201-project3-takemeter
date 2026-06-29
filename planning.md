# TakeMeter — Planning Doc

## 1. Architectural decisions

**Community:** 

I considered many communities for this classification task. Roasting communities like r/RoastMe feature criticism that is meant to be a little mean, so it would be hard to distinguish what was too much. I tried looking into r/recruiting and r/recruitinghell communities. However, for the first, it is mostly advice and personal experiences from recruiters/talent acquisition, while the second one is mostly memes and stories that complain about the recruiting process, so I didn't find patterns that would be good for a classification project. I thought about communities that specifically ask for criticism, like r/Resume and r/ArtCrit, but the criticisms are too nice (either because of subreddit filtering or because people are trying to be helpful) and I wouldn't be able to find patterns that would be good for a classification project.

I thought about a community that invited negative criticism based on subjective opinion, and criticism that was based on arguments supporting the opinion. That communities I had in mind were dating shows on Netflix like Perfect Match or Love is Blind. Some comments are meaner than others, but the problem I found is that part of the rules of these communities is to be nice since they are real contestants. I finally decided that I needed a community of fictional characters in a show that people could freely criticize without any filters.

The community I ended up choosing was r/MeanGirls. Even though there are also rules about being kind to anyone, people can condemn with more freedom the characters in the movie.

Then, after over 50 comments, I realized that I wasn't finding enough comments for the 200 sample, I had to expand to YouTube comments on the movie.

## 2. Labels & Definitions

*   **Label 1: Defense**  
    *   *Definition:* Comments that defend or rationalize a character's actions by attributing them to external factors like insecurities, retaliation, home life, or high school social pressures.
    *   *Example 1:* "I would say all Janis’s actions are a response to someone else’s actions... She never actively bullied anyone 'just because', like how Regina did."
    *   *Example 2:* "Even when she publicly humiliated Regina, it was because Regina insulted her right beforehand... she didn’t go up on stage to actively humiliate her."

*   **Label 2: Condemnation**  
    *   *Definition:* Comments that criticize a character's actions and/or personality traits directly, highlighting their toxic behavior or manipulation without excusing them from it.
    *   *Example 1:* "She's not the 'villain' but she's also a mean girl in her own way. A less successful mean girl. She manipulated a new student to do her bidding..."
    *   *Example 2:* "Essentially, Janis learns it's ok to be an asshole if you look like Lizzy Caplan."

*   **Label 3: Mixed**  
    *   *Definition:* Comments that explicitly combine both defense and condemnation within the same text block, balancing a critique of the character's flaws with a justification of their motives.
    *   *Example 1:* "Agreed - she totally used Cady and took advantage of her naivety as revenge on Regina [Condemnation]. That being said, I liked how she was one of the first people to make Cady feel welcome at school [Defense]."
    *   *Example 2:* "My only issue with her is taking advantage of a person who obviously is socially inept [Condemnation]. I get her wanting revenge I get that Regina deserves it [Defense]."
*   
*   **Label 4: Hot_Take**  
    *   *Definition:* Comments that express a strong opinion about a character/characters without providing more context or justification to their stance.
    *   *Example 1:* "Gretchen is the real villain."
    *   *Example 2:* "i feel like cady was both the protagonist and antagonist."

---

## 3. Hard Edge Cases

*   **Edge Case 1: Mixed vs. Condemnation**
    *   *Definition:* Comments where the vast majority of the text is a direct critique of a character, but the author still explicitly concedes or states that the character's motives/anger were justified (even if they don't elaborate on why).
    *   *Rule:* Label as Mixed only if the defense/justification is a substantial point or includes specific examples. If a comment is overwhelmingly negative and only includes a brief, throwaway disclaimer (e.g., "She's not the real villain but..."), label it as Condemnation.
    *   *Example:* "YES! Janis's an antagonist, sure. She has justified reasons to be angry. She goes about it the wrong way. The movie doesn’t hold her as accountable for her misdeeds the way it does for others and it’s a valid criticism. She’s NOT “the real villain.” She definitely has a toxic “it’s okay if I’m mean because I was hurt/abused, so anything I do is justified retribution” mindset that IS unhealthy AND ultimately disproven. Cady even says “ruining Regina George’s life didn’t make me any happier.”

*   **Edge Case 2: Praise/Admiration of Toxic Traits**
    *   *Definition:* Comments where the author uses negative words to describe a character's toxic behavior ("bitchy," "mean," "never apologized"), but does so with explicit praise, admiration, or validation because they find the behavior realistic, entertaining, or well-written.
    *   *Rule:* Label as Defense. Even though the behavior itself is technically "bad," the author is validating, rationalizing, and supporting the character’s choices within the context of the story, rather than criticizing them.
    *   *Example:* "THIS. I hate hate HATE when movies take the mean person and give them that “change of heart” nonsense towards the end because life is rainbows and sunshine’s and we all get along. It’s annoying 😭😭 and very unrealistic. I LOVED her consistency with her ways and structure, never bending it for ANYONE. She was still bitchy at the end, never apologized, took credit for things she shouldn’t have been able to take credit for, and didn’t get in trouble for her actions. Sure she was injured and SLIGHTLY humbled, but even then she wasn’t all “ok let’s be best buddies now and kiss and sing campfire songs in a circle”. SHE WAS STILL THE SAME WAY LIKE SHE WAS IN THE BEGINNING. I love her character design.”

## 4. Data Collection Plan

* **Source Location:** I will collect comment data directly from r/MeanGirls manually, targeting character discussion threads.
* **Target Volume:** A total of 200 annotated examples, aiming for a distribution of roughly 50 comments per label.
* **Mitigation Strategy for Underrepresentation:** If certain labels are underrepresented, I will look into other subreddits that discuss the movie.

---

## 5. Evaluation Metrics

* **Selected Metrics:** Macro F1-Score and a Confusion Matrix.
* **Why Accuracy is Insufficient:** With a compact dataset of 200 examples, natural subreddit trends can create severe class imbalances (e.g., a wave of anti-Janis threads could make *Condemnation* 70% of the dataset). A baseline model that blindly predicts "Condemnation" every time would achieve a deceptively high 70% accuracy while failing completely to identify defenses, hot takes, or mixed stances.
* **Why Macro F1-Score is Right for this Task:** Macro F1 calculates the balance of precision and recall for each of the four categories independently and averages them equally. This ensures that the model is heavily penalized if it ignores minority classes, forcing it to accurately learn the unique linguistic structures of all four labels regardless of how many examples exist in the dataset.
* **Why a Confusion Matrix is Needed:** Because our categories are highly nuanced, we need to see exactly where misclassifications happen. A confusion matrix will reveal if the model is genuinely separating pure stances (*Defense* vs. *Condemnation*) or if it is failing to identify the linguistic pivot boundaries of the *Mixed* class.

---

## 6. Definition of Success

* **Target Performance Threshold:** A Macro F1-Score of **greater than 0.75**.
* **Why this Threshold is Realistic:** Fandom discourse regarding fictional character morality is highly subjective. Because human annotators frequently disagree on where a "critique" ends and a "defense" begins, a perfect 1.0 score is statistically unrealistic. Achieving a 0.75 F1-score indicates that the model has successfully mapped the core linguistic boundaries of all four categories despite a compact 200-example dataset.
* **Real-World Utility (TakeMeter):** This performance level makes the classifier genuinely viable for deployment as a browser extension or subreddit filtering tool. An F1-score of 0.75 ensures that if a user toggles TakeMeter to "Filter out low-effort noise," approximately 75% of the comments removed will be correct. This dramatically cleans up the user's feed, hiding short, unsubstantiated claims (`Hot_Take`) while highlighting more rich and balanced community debates (`Defense`, `Condemnation`, `Mixed`).

---

## 7. AI Tool Plan

This section explains how an AI tool (like Copilot) will be used as an assistant to build, label, and check the dataset. 

### 7.1 Label Stress-Testing (Before Labeling)
Before labeling the 200 comments by hand, the AI will be used to test if the label definitions are clear enough.
* **The Plan:** The AI will be given the label definitions and told: *"Write 10 fake Mean Girls comments that are right on the line between two labels (like a comment that is half-Defense and half-Mixed)."*
* **The Goal:** If the AI writes a comment that is impossible to label cleanly, it means the definitions are too confusing. The rules will be updated and fixed before the real data is labeled.

### 7.2 Annotation Assistance (During Labeling)
An AI tool will be used as a support tool to determine if a label should be assigned.
* **The Plan:** While a human does the manual labeling, the AI will give their opinion on the label for ambiguous cases. The final dataset will be 100% human-approved.

### 7.3 Failure Analysis (After Testing the Model)
After the model is trained and tested, the AI will help figure out why the model made mistakes.
* **The Plan:** A list of all the wrong predictions will be given to the AI tool. 
* **What to Look For:** The AI will be asked to find patterns in the mistakes (for example: *"Is the model always getting confused by sarcastic comments, or comments that mention Regina George?"*).
* **Double-Checking:** Every pattern the AI finds will be manually checked against the real data to make sure it is true before writing the final report.