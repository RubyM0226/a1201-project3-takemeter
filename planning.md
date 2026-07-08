## Community

**r/glassanimals** — the fan subreddit for the band Glass Animals (oneof my favorites ever). Conversations are text-heavy, active, and varies in quality and purpose. Some comments offer specific analysis of production, lyrics, or sound design, while others are pure emotional reaction, personal anecdote, or community-facing questions with no evaluative content at all.

I read comments across five different thread types, deliberately choosing a mix rather than reading only one conversation:

1. A ZABA (2014 album) discussion/nostalgia thread
2. An April Fools "joke" post and its reactions (excluded but important to note)
3. A "does this song sound different?" question thread
4. A song-queue-order/listening-ritual thread
5. "Recommend similar artists" and "favorite/least favorite song per album" threads

ABout 55–60 comments read. 

## Scope exclusions

Two comment types were excluded from the labeling scheme because they represent a different speech act than the overall music discussion:

- **AMA-style direct address to the band** (questions or messages addressed to band members rather than to fellow fans) (these are requests/address, not commentary)
- **Joke/novelty posts and their reactions** (an April Fools prank thread- number two) (reactions here respond to being pranked, not to the music)

## Labels

### 1. Substantive Critique/Analysis
**Definition:** Makes a specific, checkable claim about a musical, production, or lyrical element of the band's work, and gives some reasoning or evidence for it.

- Example 1: *"Sometimes, you're heart rate will effect how fast you perceive the song to be... pork soda is a very audibly complex song... you might be uncovering new layers to the song with your re-listening."* 
- Example 2: *"On The Run — the chorus is honestly offensive to my ears and the ending of the song makes me feel like I'm having an anxiety attack."* 
- Uncertain case: *"I love how on ILYSFM they have Creatures in Heaven, Wonderful Nothing, and A Tear in Space in succession, so I start with that, then Vampire Bat (such a bop)..."* 

### 2. Reaction/Opinion
**Definition:** States a preference, ranking, or emotional response to the music with no specific supporting reference to a musical/production/lyrical element.

- Example 1: *"Zaba is by far my fave album!"*
- Example 2: *"Mama's Gun. I know some people love this song but I just find it so boring."* 
- Uncertain case: *"I've been extremely disappointed by how they've moved away from the ZABA sound... nothing else I've ever heard comes close to its sound."*

### 3. Personal Anecdote/Experience
**Definition:** Centers on the commenter's own history, memory, or relationship with the album/band rather than evaluating the music's qualities.

- Example 1: *"Found them at zaba, and saw them for that tour. Best show of my life... Zaba is in another stratosphere."*
- Example 2: *"Been a zaba fan since 2014. Made my high school bf at the time listen to it... his response was: 'this sounds like music for stoners.'"*
- Uncertain case: *"Found them at zaba... best show of my life. I still think it's their best album."* 

### 4. Discussion/Question
**Definition:** An open-ended question, observation, or shared practice directed at the fan community (not the band) that doesn't stake an evaluative claim about music quality, express a clear reaction, or recount personal history.

- Example 1: *"Does Pork Soda sound odd?... I might be going crazy but I have to know if I am the only one."*
- Example 2: *"What songs do you move around in the queue to listen to in a specific order? Mine are Exxus -> Golden Antlers -> Cocoa Hooves..."*
- Uncertain case: *"I listen to more electronic artists usually like Odesza, Flume, Bon Iver... Not a ton of similar artists to glass animals but they're just something special."* 

## Tie-break rule (for mutual exclusivity)

Applied in order:
1. If the comment addresses the band directly or responds to a joke/prank post → **excluded from scope**.
2. If the comment contains at least one specific, checkable reference to a musical/production/lyrical element (a song title + a reason, a production detail, a lyric) → **Critique**, even if wrapped in hype or emotional language.
3. If the comment centers on the commenter's own history/story with the album or band → **Anecdote** 
4. If the comment is a quesGtion or shared practice/list directed at the community with no evaluative claim → **Discussion/Question**.
5. Otherwise (preference, ranking, or emotional stance with no specific reference) → **Reaction**.

This ordering was tested against 10 comments spanning all four labels (including the uncertain cases above) and resolved each to exactly one label.

##  Hard edge cases

The first edge case is (that should be more difficult to pass) is when a comment that wraps a specific musical idea inside an anecdote or question frame. This is like the example: "remember when they played Gooey at that tiny venue in 2015 and the sound system blew a speaker? worth it tho, best mix I've heard live." This reads structurally like an anecdote but ends with a specific, checkable audio-quality claim. Deciphering which label comments like these will receive is much more difficult. When a comment contains examples of two different labels and at least one specific, checkable reference to a musical element, the specific claim takes priority. 

## Data collection plan

I'll collect from r/glassanimals across multiple thread types (discussion threads, "rank/underrated" threads, recommendation threads, first-listen reaction threads on new releases), avoiding over-sampling from any single thread so labels aren't skewed. I'm excluding two categories entirely from collection: direct address to the band (like the AMA-style questions/messages above) and reactions to joke/novelty posts (like the April Fools threads above), since both do not fit in any of my four labels.

Target: 200 total examples, aiming for roughly 50 per label. Reaction and Critique appear most common. I will need more diverse types of threads to find Anecdote and Discussion/Question comments.

If a label is underrepresented after collecting 200 total: I will not force-fit borderline examples just to pad a count. Instead I'll do targeted collection from thread types to produce that label rather than continuing to pull randomly from threads that have already been exhausted for that category. If a label still can't reach a reasonable minimum, I'll document this limitation rather than inflating the count.

## AI Tool Plan

Label stress-testing: Before annotating, I gave Claude my four label definitions and the edge-case description above and asked it to generate 8 boundary posts. Several exposed that my original tie-break rule needed to be stated as a general priority rule rather than handled case-by-case. I've incorporated that into my edge-case handling rule above. I'll re-run this stress test with a fresh batch of AI-generated boundary posts after I've annotated my first 50 real examples, to catch any additional edge cases.

Annotations: I will be using Claude to pre-label a first pass of each batch of 200 collected comments, but I will review and correct every pre-label myself. I'll track this by keeping a pre_labeled boolean column in my annotation spreadsheet/CSV, set to true for any row where the AI suggested a label first, so I can disclose in my AI usage section exactly which portion of my dataset was AI assisted.

Failure analysis: After training and evaluating my model, I'll compile the list of misclassified examples and give that list to Claude, asking it to identify recurring patterns. I will not accept these patterns at face value. I'll manually review a sample of the flagged errors myself to confirm the pattern actually holds before writing it up in my evaluation.


## Why this matters to the community

r/glassanimals is often assumed to be uniformly nostalgic or uncritical, since fandom spaces skew toward affection for the artist. Separating critiques from reaction, personal storytelling, and community-focused questions reveals that even a tight-knit single-artist fan space produces real, specific music analysis. This could be across production choices, lyrical interpretation, cross-album and cross-artist comparison alongside nostalgia, emotional attachment, and casual fan-to-fan exchange. Glass Animals have so many different communities and its important to represent them all.