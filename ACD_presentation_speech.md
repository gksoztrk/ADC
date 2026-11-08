# Speech for "Stars, words and friends" — slide by slide

This file goes with `ACD_project_final_presentation.pptx` (29 slides). For every slide you get four parts:

1. **What to say** — a speech you can read or learn. It is short enough for about 1–2 minutes.
2. **Explained very simply** — the same idea, slowly, with small everyday examples. This part is for *you*, so that you really understand each step.
3. **The numbers on the slide** — what every number means.
4. **If someone asks** — likely questions and simple answers.

Section and title slides (1, 3, 7, 13, 18, 23, 26, 29) only get a short speech.

---

## Before you start: the words you will need

Read this once. Every hard word in the talk is explained here in plain language.

| Word | What it means, simply |
|---|---|
| **Review** | A short text a person writes about a shop or restaurant on Yelp, together with **stars** from 1 (very bad) to 5 (very good). |
| **Mean** | The average. Add all the numbers and divide by how many there are. |
| **Median** | The middle number when you line the numbers up from small to big. Half are below, half are above. |
| **Category** | A group of similar businesses, for example "Restaurants" or "Beauty & Spas". We use 12. |
| **Forecast** | A guess about the future, made only from what we know today. |
| **Baseline** | The simplest possible guess. A fancy model is only good if it beats this simple guess. |
| **Error** | How far the guess was from the truth. If we guessed 4.0 stars and the truth was 4.2, the error is 0.2. |
| **MSE** | "Mean squared error". Take every error, multiply it by itself (so big mistakes count much more), then take the average. Smaller is better. |
| **RMSE** | The square root of MSE. It brings the error back to "stars", so you can read it as "we are wrong by about this many stars". |
| **"x % lower MSE"** | How much smaller the squared error is compared with another model. 5 % lower MSE is a small gain (it is only about 2.5 % lower RMSE). |
| **p-value** | A number from 0 to 1 that answers: "if there were really no difference, how surprising would our result be?" Small (below 0.05) = probably a real difference. Big = it could easily be luck. |
| **Confidence interval (CI)** | A range where the true value probably lies. If the range includes 0, we cannot say there is a difference. |
| **Significant** | The p-value is small, so the difference is probably not luck. It does **not** mean "big" or "important". |
| **Correlation** | A number from −1 to +1 that tells if two things go up together (+), go in opposite directions (−), or have nothing to do with each other (0). |
| **Placebo** | A fake version of something, used as a control. In medicine it is a sugar pill. Here it is a fake ("scrambled") friendship network. If the fake works as well as the real one, the real one did nothing special. |
| **Noise** | Random wobbling that has no meaning. If 5 people rate a restaurant, the average jumps around just because of *which* 5 people came. |
| **Noise floor** | The smallest error any model could ever reach, because of noise. Nobody can go below it. |
| **Walk-forward** | A fair way to test forecasts: stand at one month, use only the past, guess the next month. Then step one month forward and do it again. |
| **Look-ahead** | Cheating by using information from the future. We never allow it. |

---

## Slide 1 — Title: "Stars, words and friends"

### What to say
"Hello everyone. Our project is called *Stars, words and friends: forecasting Yelp ratings with text and social networks*. We used the Yelp Open Dataset, and we kept only the businesses in the state of California, which in this dataset is really the Santa Barbara area. We look at three things: the **stars** people give, the **words** they write, and the **friends** they have on Yelp. Our main question is: can we forecast the monthly ratings of groups of businesses, and does the friendship network help, beyond the rating history and the review texts?"

### Explained very simply
Imagine a big notebook where people write about restaurants and shops. Each note has a little score from 1 to 5 stars, and some words. Some of the writers are friends with each other. We want to know two things:
- Can we guess next month's average score?
- Does knowing **who is friends with whom** help us guess better?

### Before presenting
Replace the placeholder "[Author names and student numbers]" on this slide and on slide 29.

---

## Slide 2 — Contents

### What to say
"Here is our plan. We have six parts. First, the **data** and a first look at it. Second, the **text**: how we turned words into sentiment. Third, the **social network**: who is friends with whom. Fourth, the **forecasting**: guessing next month's stars. Fifth, the **integration**: does the network help the forecast? And sixth, our **conclusions**. All the way through, we keep one question in mind: does the reviewers' social network add anything beyond the rating history and the review text?"

### Explained very simply
This slide is just the map of the trip. Each box is one stop:
1. Data and exploration — slides 3–6
2. Text and sentiment — slides 7–12
3. Social network — slides 13–17
4. Forecasting — slides 18–22
5. Integration — slides 23–25
6. Conclusions — slides 26–28

---

## Slide 3 — Section 1: Data and exploration

### What to say
"Let's start with the data: what it is, the rules we followed to keep our tests honest, and a first look at the reviews."

---

## Slide 4 — Data: Yelp, Santa Barbara area

### What to say
"After cleaning, we have **348,014 reviews**, written between March 2005 and January 2022, about **5,164 businesses**, by **155,252 users**. Each business is placed in one of 12 big categories, like Restaurants or Beauty & Spas. The average rating is 3.96 stars, but the median is 5: most people give 5 stars. Two thirds of the users wrote only one review. Inside this group of users there are **471,089 friendships**.

We made a few careful choices. We removed cities with fewer than 20 businesses, because they are outside the area. Yelp writes the year 2020 in the Elite list as '20,20', so we repaired it; this way we know if a person was 'Elite' at the moment they wrote each review. And from the user file we only take the **friendship network**. Numbers like 'fans' or 'average stars' describe the user at the very end of the data, so using them would be like peeking into the future. We do not use them as forecast inputs."

### Explained very simply
- **Cleaning** means tidying up the data before using it, like sorting toys before playing. We removed repeated reviews (348,856 became 348,014) and businesses that are far away (like Reno or Truckee).
- **Macro category**: each business can have many labels ("Pizza, Italian, Bar"). We give it **one** main group, so we can compare groups.
- **Elite**: Yelp gives a special "Elite" badge to very active reviewers each year. The file had a typo ("20,20" instead of "2020"), so we fixed it. About 12.5 % of reviews were written by someone who was Elite that year.
- **Why not use "fans" or "average stars"?** Those numbers were measured at the *end* of the data (2022). If we used them to guess something in 2018, we would be using information from the future. That is cheating, so we don't.
- **Friendship edges**: an "edge" is one friendship line between two people. 471,089 friendship lines connect the users in our data. About 41 % of users have at least one friend here, and their reviews are 55 % of all reviews.
- **J-shaped**: if you draw how many reviews have 1, 2, 3, 4, 5 stars, the picture looks like the letter J: a few 1-star reviews, very few 2 and 3, and a huge number of 5-star reviews.
- **Cached**: the slow steps save their results on disk, so the second time the notebook runs in minutes instead of 2–3 hours.

### The numbers on the slide
| Number | Meaning |
|---|---|
| 348,014 | reviews we use |
| 5,164 | businesses |
| 3.96 | mean stars (median 5) |
| 155,252 | users who wrote these reviews |
| 66.7 % | users who wrote only one review |
| 471,089 | friendships between these users |

### If someone asks
- *"Why only California?"* — The full dataset is huge. California in this dataset is one compact area (Santa Barbara), which keeps the network and the time series manageable.
- *"Why do you not use the user's average stars?"* — Because it is computed with all the user's reviews, including future ones. It would leak the future into the forecast.

---

## Slide 5 — Rules and test design

### What to say
"We followed four rules all the time.

**One: no look-ahead.** Everything is split by time. A forecast only uses reviews written before its date. Look at the timeline: the text model learns only from reviews before 2017. We choose the settings of our models on 2014 to 2017. And we test on 2018 to 2021, which we never touched while building the models. The orange line marks the start of COVID in March 2020, so we report results before and during COVID separately.

**Two: no self-leakage.** A review's own text almost tells you its stars. So we never use a review's own sentiment to predict its own stars. Sentiment only enters through earlier reviews.

**Three: baseline first.** A model only counts if it beats a simple guess on data it has not seen.

**Four: association is not causation.** If friends rate alike, it could be influence, similar taste, or simply visiting the place together. Every test says what it can and cannot rule out."

### Explained very simply
- **No look-ahead** is like a weather forecaster who is not allowed to look out of tomorrow's window. Our models only see the past.
- **The three time blocks** (the picture on the slide):
  - *Learning the words* (before 2017): the computer learns which words mean "happy" and which mean "unhappy".
  - *Tuning* (2014–2017): we try a few settings, like turning the knobs of a radio, and keep the best ones.
  - *Testing* (2018–2021): the real exam. 48 months, guessed one at a time. The models never saw these months before.
- **Walk-forward**: we stand in December 2017, guess January 2018. Then we step to January 2018, learn from it, guess February 2018. And so on, 48 times.
- **No self-leakage**: if a review says "the worst place ever", of course it has 1 star. Using its words to "predict" its stars would be too easy and useless for the future.
- **Baseline first**: if your fancy method can't beat "just guess the average", it is not useful.
- **Association is not causation**: two friends both love a restaurant. Did one convince the other (influence)? Do they just like the same food (taste)? Or did they go together (joint visit)? Data alone often can't tell.

---

## Slide 6 — Exploring the reviews

### What to say
"Here is a first look at the reviews. The number of reviews grew until about 2018, then dropped sharply with COVID in 2020. Stars are J-shaped: the mean is 3.96 but the median is 5, and the average stars per month barely move over the years.

There is a very long tail: the median business has only 20 reviews and the median user only 1. The 802 businesses with at least 100 reviews hold 70 % of all reviews. If we split the differences between single ratings, businesses explain only about 21 % and users about 26 %. So a single rating is very noisy.

That is why we don't forecast single businesses. We **group businesses into categories** and forecast the **monthly mean stars of each category**."

### Explained very simply
The picture has six small panels:
- **(a) Reviews per month**: a hill that grows until 2018, then a big drop in 2020 (COVID, red line).
- **(b) Mean stars per month**: an almost flat line. Stars change very slowly.
- **(c) Stars are J-shaped**: tall bar at 5, smaller at 4, tiny at 2 and 3, a bit at 1.
- **(d) Long tail**: very few businesses and users have many reviews; most have very few.
- **(e) Unhappy reviewers write more**: 1- and 2-star reviews are longer. Angry people explain more.
- **(f) Mean stars by category**: some groups get higher stars than others.

**"Businesses explain 21 % of the variance"** means: if you know *which business* a review is about, you can explain only about one fifth of why its stars are high or low. The rest is the person, the day, luck. So one rating alone is a very wobbly signal. Averaging many ratings together makes the wobbling smaller, and that is why we forecast **averages of categories**.

### If someone asks
- *"Why not forecast each business?"* — A business gets only a few reviews per month (often 0–4). Its monthly average jumps around randomly. A category, like Restaurants, gets hundreds or thousands, so its average is much steadier.

---

## Slide 7 — Section 2: Text and sentiment

### What to say
"Now the text. We go from raw review text to sentiment, then to aspects — *what* people talk about — and finally to the gap between what people write and the stars they give."

---

## Slide 8 — Text processing

### What to say
"First we clean the text: lower case, remove links and extra spaces, filter languages, split into words, and turn the words into numbers. Instead of guessing which cleaning steps help, we **tested** six versions with the same classifier, trained on reviews before 2017 and tested on 2017 to 2018.

The winner is **TF-IDF with bigrams and negation tags**, with a macro-F1 of 0.735, against 0.694 for plain TF-IDF. Bigrams are pairs of words, like 'not good'. Negation tags mark the word after 'not', so 'not good' becomes 'not_good' and the model doesn't confuse it with 'good'.

Removing stop words *hurts* (0.681), because the stop-word list deletes 'not'. Lemmas — turning 'tasted' into 'taste' — add almost nothing for this job (0.697). But both are still useful later, because they make topics easier to read."

### Explained very simply
Computers can't read words, they only understand numbers. So we must turn each review into a list of numbers. There are many ways to do it, and we tried six:

| Version | What it does, simply |
|---|---|
| Counts, unigrams | Count each single word: "good" appears 2 times, "pizza" 1 time… |
| TF-IDF, unigrams | Like counting, but words that appear everywhere (like "the") get less weight, and rare, telling words get more. |
| + stop-word removal | Throw away very common small words ("the", "and", … but also "not"!). |
| + lemmas | Turn each word into its basic form: "tasted", "tastes" → "taste". |
| + bigrams | Also count pairs of words: "not good", "highly recommend". |
| + bigrams + negation tags | Glue "not" to the next word: "not good" → `not_good`. |

**Why removing stop words hurts**: "The food was not good" without stop words becomes "food good". The meaning flips! That's why it is worse.

**Macro-F1** is a score from 0 to 1 for how well the computer sorts reviews into negative (1–2 stars), neutral (3 stars) and positive (4–5 stars). "Macro" means each of the three groups counts the same, so the model can't cheat by only being good at the big "positive" group.

**The classifier** is a logistic regression: a simple model that learns a weight for every word (positive words push towards 5 stars, negative words towards 1 star).

### The numbers on the slide
- 0.735: best version (bigrams + negation tags)
- 0.694: plain TF-IDF
- 0.681: with stop words removed (worst of the TF-IDF versions)
- 0.697: with lemmas
- Training: 30,000 reviews before 2017. Test: 10,000 reviews from 2017–2018.

---

## Slide 9 — Which sentiment model?

### What to say
"Next, we compared five sentiment models on the **same 6,000 reviews written in 2019 or later**, so in the future of all training data. We used the stars as a rough label: 1–2 stars negative, 3 neutral, 4–5 positive.

The winner is the simple model trained on Yelp itself: **TF-IDF with logistic regression**, with a macro-F1 of 0.747 and an average error of 0.42 stars. The transformers come behind: RoBERTa 0.668, DistilBERT 0.661, MiniLM 0.650. VADER, a word-list method, is last at 0.588.

But this race is not completely fair. Our TF-IDF model learned from about 171,000 Yelp reviews. MiniLM's classifier learned from 15,000. And the pretrained transformers were not retrained on Yelp at all — they only got a small calibration step — because retraining needs a GPU. So the fair conclusion is: **a simple model trained on the right kind of data beats bigger models trained on other data**, not that TF-IDF beats transformers in general. All models found the neutral 3-star reviews the hardest."

### Explained very simply
We had five "readers" read the same 6,000 reviews and guess whether each was happy, neutral or unhappy:

| Reader | What it is, simply |
|---|---|
| **VADER** | A dictionary of words with scores ("great" = +, "awful" = −). No learning. Fast. |
| **TF-IDF + logistic regression** | Our own simple model, which learned from old Yelp reviews which words go with which stars. |
| **MiniLM + logistic regression** | A pretrained model turns each review into 384 numbers that capture meaning; then a simple model learns from those numbers. |
| **DistilBERT** | A big pretrained "transformer" trained on movie-review sentences. |
| **RoBERTa** | A big pretrained transformer trained on tweets (negative, neutral, positive). |

- A **transformer** is a large modern AI model that reads words in context. It is smart, but it was trained on *other* kinds of text (movies, tweets), and it is slow on a normal computer (10–70 reviews per second).
- **Calibration**: the pretrained models give their own kind of score. We add a tiny step (trained on 2,000 older reviews) that translates their score into "expected stars", so all five readers speak the same language.
- **Why our simple model wins**: it learned Yelp's own words. It's like a local guide who knows the local slang beating a famous visitor.
- **Neutral is hardest**: a 3-star review usually mixes praise and complaints ("food good, service slow"), so it is hard to put in one box. The best model recognised only about half of them.
- **Avoiding a leak**: reviews before 2017 get their TF-IDF score from a model that did not see them (5-fold cross-fitting: split them into 5 parts, score each part with a model trained on the other 4). This matters because these scores are later used as forecast inputs.

### The numbers on the slide
- 0.747 macro-F1 for TF-IDF + LR (95 % range 0.733–0.763), error 0.42 stars on average
- RoBERTa 0.668, DistilBERT 0.661, MiniLM 0.650, VADER 0.588
- 171,000 Yelp reviews used to train TF-IDF + LR

### If someone asks
- *"So transformers are worse?"* — No. Here they were used off the shelf, without retraining on Yelp. A fine-tuned transformer would probably win; we would need a GPU for that.

---

## Slide 10 — Aspect-based sentiment

### What to say
"Overall sentiment tells us *that* a review is negative. Aspect-based sentiment tells us *why*: for example, 'food fine, service terrible'. We defined six aspects: food, service, price, ambience, wait and cleanliness. We checked them with a topic model, NMF: 9 of its 14 topics match one of our aspects. Then VADER scored each **sentence** that mentions an aspect. We chose VADER here because it needs no stars to learn, so it cannot leak ratings into later models.

On the left: food is mentioned in 65 % of reviews and service in 54 %; cleanliness in only 10 %. On the right we compare 1-star and 5-star reviews. Service and cleanliness separate them most: gaps of 0.75 and 0.71. Wait has the smallest gap, 0.35.

We checked VADER against a dedicated aspect model, DeBERTa. They agree on 56 % of the sentence-aspect pairs, and for wait the agreement is basically zero. That is because complaints about waiting and price are often written as facts — 'we waited 40 minutes' — and a word list can't see that this is a complaint."

### Explained very simply
- **Aspect** = one thing about a business that people talk about: the food, the staff, the price…
- **How we find them**: we use a list of words for each aspect. For example, "waiter", "staff", "rude", "friendly" → service. If a sentence contains one of these words, it is about that aspect.
- **NMF topics**: a method that reads many reviews and finds groups of words that often appear together, *without* being told what to look for. Its groups ("order, chicken, salad, burger…", "friendly, staff, helpful…", "price, reasonable, worth…") match our aspects, so our word lists are sensible.
- **VADER scores each sentence** from −1 (very negative) to +1 (very positive). A review's score for "service" is the average over its sentences about service.
- **The picture on the right**: the orange dot is the average score in 1-star reviews, the dark dot in 5-star reviews. The longer the line between them, the more that aspect separates happy from unhappy customers. Service has the longest line: when people are unhappy, it is often about the staff.
- **The check with DeBERTa**: DeBERTa is an AI model built exactly for aspect sentiment. We gave it 3,000 sentences (500 per aspect). It agreed with VADER on 56 % of them (kappa 0.25, which is "modest"). DeBERTa was a little better at predicting stars (0.45 against 0.41), and much better for wait and price. But it is slow (about 26 sentences per second), so it could not score all the 700,000+ pairs; it is used as a check.
- **Kappa**: an agreement score that removes agreement by pure luck. 0 = no better than chance, 1 = perfect agreement.

### The numbers on the slide
- Mentions: food 65 %, service 54 %, price 30 %, wait 30 %, ambience 22 %, clean 10 %
- Gaps between 1-star and 5-star reviews: service 0.75, clean 0.71, food 0.51, ambience 0.49, price 0.45, wait 0.35
- 56 % agreement with DeBERTa; kappa near 0 for wait

### If someone asks
- *"Why are the 1-star scores close to zero, not very negative?"* — VADER only reacts to emotional words. Many complaints are factual ("we waited 45 minutes", "$30 for a burger"), so they score about 0. Only service and cleanliness are clearly negative (−0.15), partly because their word lists contain negative words like "rude" and "dirty".

---

## Slide 11 — What drives the stars

### What to say
"Which aspects matter most for the stars? We ran a regression of each review's stars on its six aspect scores, separately for each category.

**Service drives stars everywhere.** In restaurants, one standard deviation better service sentiment goes with 0.52 more stars, against 0.45 for food. In shopping it reaches 0.99.

**Context decides the rest.** Food matters where food is the product. Price matters where the bill is large or uncertain, like home services, 0.55. Cleanliness matters most in hotels, 0.52.

**Mentioning is a warning sign.** Even a neutral mention of service, price or cleanliness goes with 0.5 to 0.7 fewer stars: people bring these topics up mostly when something went wrong.

Stars and text are written at the same time by the same person, so these are associations, not causes, and these scores are never used as forecast inputs."

### Explained very simply
- **Regression**: a method that finds how much each thing "pushes" the stars up or down, while holding the other things fixed.
- **"Per standard deviation (SD)"**: the standard deviation is the typical size of a difference. "One SD better service sentiment" means "service described clearly more positively than usual". We use SD so all six aspects are measured on the same ruler.
- **Panel (a)** — the blue table: each cell says how many stars go with one SD better sentiment about that aspect, in that category. Dark blue = strong.
- **Panel (b)** — the red/blue table: what happens when an aspect is *mentioned at all*, in neutral words. Red = fewer stars. Service −0.72, price −0.59, cleanliness −0.54 overall. Happy customers rarely write about the bill or the bathroom; unhappy ones do.
- **Panel (c)**: aspect sentiment year by year. It rose slowly until about 2019 and dropped in 2020–2021 (COVID: takeout, closed dining rooms, staff shortages are possible reasons, not tested).
- **Why not causes**: the person writes the text and picks the stars at the same moment. We can't say the text *caused* the stars.

---

## Slide 12 — The sentiment-star gap

### What to say
"The gap is the stars we would expect from the text minus the stars actually given. A negative gap means the text is gloomier than the rating: a polite 4 stars with an unhappy text. Our idea: if people soften their stars but not their words, a falling gap could warn that ratings will drop.

Before using it, we looked at how it behaves. First, the mean gap is +0.78 at 1 star and −0.32 at 5 stars. That is mostly **regression to the middle**: a model rarely predicts the extremes. So we never compare the gap across star levels, only within a category over time. Second, the gap drifts slowly down, from +0.07 in 2010 to −0.20 for Elite reviewers in 2021. Either texts became more measured, or our text model, trained before 2017, is ageing. The data can't tell which.

The real forecasting test of this idea comes later, in the ARIMA models."

### Explained very simply
- **Gap = what the words say − what the stars say.** Example: someone writes "it was okay, a bit slow, food average" (words say about 3) but gives 4 stars. Gap = 3 − 4 = −1: the words are gloomier than the stars.
- **Regression to the middle**: our text model is a cautious guesser. For a 1-star review it might guess 1.8; for a 5-star review it might guess 4.7. So the gap is automatically positive for 1-star reviews and negative for 5-star reviews. It's not people being strange; it's the model being cautious. That's why we only compare a category with *itself* over time.
- **The drift**: over the years the gap goes down slowly, especially for Elite reviewers. Maybe Elite reviewers write long, balanced texts but still give high stars. Or maybe our model, which learned on old reviews, slowly gets out of date. We can't separate the two.
- **How it is used later**: the forecast uses `gap_dev3` — the gap of the last 3 months *minus* the gap of the last 12 months. Subtracting removes the slow drift and keeps only recent changes.

---

## Slide 13 — Section 3: Social network

### What to say
"Now the social network: who is friends with whom, how the network is organised, and whether friends rate alike."

---

## Slide 14 — The friendship network

### What to say
"Users are the points, and friendships are the lines between them. We keep only friendships where both people are in our data. **64,154 users** have at least one friend here, out of 155,252. They are connected by **471,089 friendships**, and **96.3 %** of them belong to one big connected group.

The network is a **small world**. The average clustering — how often two of my friends are also friends with each other — is 0.098, about five times what we get in a random network with the same number of friends per person (0.020). Yet the average distance between two people is short: 4.26 steps, against 3.72 in the random network.

The Louvain method finds 1,676 communities with a clear structure (modularity 0.526). But if we run it again with a different random start, the groups change somewhat (adjusted Rand index 0.57), so communities are useful groupings, not exact facts.

The median user has 4 friends; the most connected has 2,007. Two limits: we only have one snapshot of friendships with no dates, and friends outside the area are missing."

### Explained very simply
- **A network** is like a drawing of dots (people) and lines (friendships).
- **Connected component**: a group where you can walk from anyone to anyone along friendship lines. 96.3 % of the connected users are in one giant group.
- **Clustering**: "Are my friends also friends with each other?" In real life, often yes (your school friends know each other). Here it is 0.098, five times more than random. So friendships form little circles.
- **Path length**: how many friendship steps you need to reach someone. 4.26 on average: "a friend of a friend of a friend of a friend". This is the famous "small world" idea.
- **Random network with the same degrees**: we shuffle the lines but keep everyone's number of friends. It is our "nothing special" comparison.
- **Louvain communities**: a method that finds groups of people who are much more connected inside the group than outside.
- **Modularity (Q)**: a score for how clear the groups are. 0 = no groups at all; 0.3 and above = clear structure; 0.526 is clearly structured.
- **Adjusted Rand index (ARI)**: how similar two groupings are. 1 = identical, 0 = like random. 0.57 means the two runs agree a fair amount, but not completely.
- **Degree**: how many friends one person has. Most have few, a few have very many ("hubs").

### The numbers on the slide
| Number | Meaning |
|---|---|
| 64,154 | users with at least one friend in the data |
| 471,089 | friendships |
| 96.3 % | share of connected users in the biggest group |
| 0.098 | clustering (random: 0.020) |
| 4.26 | average steps between two users (random: 3.72) |
| 0.526 | modularity of 1,676 communities |

---

## Slide 15 — Homophily: who befriends whom

### What to say
"Do friends resemble each other? We measure **assortativity**: the correlation of a trait between the two ends of each friendship. To know if a value is large, we compare it with a fake world where the traits are shuffled among users 100 times, keeping the network fixed.

Friends match strongly on **activity**: the year they joined Yelp, 0.495; being Elite, 0.452; number of friends, 0.298. People join in waves and befriend those who joined with them.

But friends match only very weakly on **opinions**: mean stars 0.053, mean sentiment 0.055. A correlation of 0.05 explains only about 0.3 % of the variation. For service sentiment it is 0.008, basically nothing.

Careful: everything here is 'significant', because with 100,000 to 471,000 friendships even tiny correlations pass the test. **Significant is not the same as large.**"

### Explained very simply
- **Homophily** = "birds of a feather flock together". Do similar people become friends?
- **Assortativity** = take every friendship, look at the two friends, and ask: if one is high on this trait, is the other one high too? It's a correlation: 1 = always the same, 0 = no link.
- **Null model**: we shuffle the traits randomly among people, like dealing cards again, but keep the same friendships. This shows what we would see *by chance*. Here the chance value is about 0.
- **z vs null**: how many "chance-sized steps" the real value is above chance. Above 2 = probably not chance.
- **Activity**: join year (0.50) and Elite (0.45) are strongly shared. Example: Elite users meet at Elite events and become friends.
- **Opinions**: 0.05 is tiny. Knowing your friends' average rating tells you almost nothing about yours. 0.05 × 0.05 = 0.0025 = 0.25 %, so about 0.3 % of the variation.
- **Why "significant" isn't "large"**: with hundreds of thousands of friendships, even a speck of dust is measured precisely. The test tells you "not exactly zero", not "important".
- **For opinions**, only users with at least 3 reviews are used, because an average of 1–2 reviews is too noisy.
- **Caveat**: these traits summarise all of a user's reviews, even ones written after the friendship. So we can't tell "chose similar friends" from "became similar to friends".

### If someone asks
- *"Why does this matter for the forecast?"* — It warns us early: if friends barely share opinions in general, we should not expect the network to change ratings much.

---

## Slide 16 — Communities and central users

### What to say
"The six biggest communities, each with at least 5,000 reviews, hold 52 % of all reviews. They differ in **who they are**: two are older, Elite-heavy groups where 40 to 45 % of reviews are by Elite users; one leans to hotels and travel and has few reviews per user, which suggests visitors; and the largest has only 5 % Elite reviews. But their opinions about aspects differ very little, by at most about 0.04 on a scale from −1 to +1.

Hubs are not opinion leaders. All the centrality measures say almost the same thing: number of friends correlates 0.98 with core number and 0.92 with PageRank. And centrality is unrelated to the stars a person gives, between −0.01 and −0.05.

The degree distribution has a heavy tail: about 2 % of users have 100 or more friends. And heavy reviewers rate a little lower: a correlation of −0.19 between number of reviews and stars."

### Explained very simply
The picture has three panels:
- **(a) Degree distribution**: for each number of friends k, the share of users with at least k friends. Both axes use a log scale (each step is ×10). The line falls slowly, in a nearly straight line: many people have few friends, a few people have huge numbers. About 27 % have at least 10 friends, 11 % at least 30, about 2 % at least 100, and only 0.03 % at least 1,000.
- **(b) Community aspect sentiment**: each cell shows how much a community's opinion about an aspect differs from everyone's average. All cells are tiny (−0.036 to +0.037). Communities are different *people*, but they think about the same.
- **(c) Centrality and behaviour**: correlations between network measures and behaviour.
  - **Degree** = number of friends. **Core number** = how deep you are inside a tightly connected core. **PageRank** = you are important if important people are your friends (the idea Google first used for web pages). **Betweenness** = how often you are on the shortest path between two other people (a "bridge").
  - They all correlate 0.78 or more with each other: a person with many friends is central in every sense. Degree alone tells most of the story.
  - Centrality and stars: about 0 — central people don't rate higher or lower.
  - Heavy reviewers rate slightly lower (−0.19): experienced reviewers are a bit more critical.

**Main message**: the network's structure is about *social activity* (who is active, who is Elite, who is a visitor), not about *opinions*.

---

## Slide 17 — Do friends rate alike?

### What to say
"Now the key network test: do friends rate alike? Yelp doesn't record who read whose review, so we must infer it carefully.

For every review written after a friend reviewed the same business, we take its **deviation** — its stars minus the business's earlier average. We pair it with a **matched stranger**: a non-friend who reviewed the same business as close in time as possible to the friend. Then we measure how much each one's deviation predicts the person's own deviation, with business and quarter fixed effects, and controlling for how generous each person usually is.

The friend's coefficient is **0.127**, the stranger's only **0.010**, for reviews at least one day apart, with p below 0.001. Same-day reviews reach 0.90, almost identical ratings, but those are probably friends who went together, so we don't count them as influence.

The effect **fades with time**: 0.37 within 30 days, 0.11 at 1 to 6 months, and about 0.05 to 0.08 after that. And a friend of a friend is no closer than a stranger (p = 0.50). This points to something specific to direct friends, not to general similar taste. But it is compatible with influence without proving it: joint visits written on different days are still possible."

### Explained very simply
- **Deviation**: if a restaurant usually gets 4.0 stars and you give 5, your deviation is +1. Deviations remove the "this restaurant is just good" part.
- **The question**: when your friend rated a place *above* its usual level, do you also rate it above?
- **Matched stranger**: a fair comparison person. Same restaurant, almost the same time, but *not* your friend. If you copy the stranger just as much, it's not about friendship; it's about the restaurant having a good month.
- **Coefficient 0.127**: if your friend rated 1 star above the usual, you rate about 0.13 stars above the usual, on average. For the stranger: 0.01, almost nothing.
- **Fixed effects**: we compare reviews *within* the same business and the same quarter, so differences between businesses or periods can't fool us.
- **Generosity control**: some people always give 5 stars. We take that into account.
- **The bar chart**: dark bars are friends, light bars are strangers, for different time gaps since the friend's review. Friends' bars are tall at first and shrink over time. Strangers' bars stay near 0 (a small 0.044 at 1–30 days, which reflects things that affect everyone at that time, like the restaurant having a good or bad month).
- **Why the fading matters**: if it were only "similar taste", it shouldn't matter *when* the friend wrote. Influence fades as you forget. So fading points towards influence (or towards going together).
- **Friend of a friend**: in 19.6 % of pairs, the "stranger" is actually a friend of a friend. They behave like a total stranger (+0.011, p = 0.50). So the link is only with *direct* friends.

### The numbers on the slide
- 0.127 friend vs 0.010 stranger (1+ days, 30,937 pairs)
- 0.90 same day (933 pairs, probably joint visits)
- 0.37 (1–30 days), 0.11 (31–180 days), 0.05 (181–730 days), 0.08 (over 2 years)
- Friends of friends: p = 0.50

---

## Slide 18 — Section 4: Forecasting

### What to say
"Now the forecasting part. We forecast the monthly mean stars of 12 business categories, with simple baselines, ARIMA-type models, and global machine-learning models."

---

## Slide 19 — Forecasting setup

### What to say
"The target is the **mean stars of a category in the next month**, for 12 categories. A single business has too few reviews for a reliable monthly mean, but since 2012 every category has at least 15 reviews every month, and Restaurants about 1,500.

We test with **walk-forward**: refit every month, forecast the next one, over 48 test months from 2018 to 2021.

We use three groups of inputs, all built only from reviews up to the forecast date:
- **History**, 17 inputs: stars this month and the two before, 3- and 12-month means, trend, same month last year, volume, spread, the overall market level and the season.
- **Sentiment**, 30 inputs: text sentiment, the sentiment-star gap and VADER over 1, 3 and 12 months, and the six aspect scores and their changes.
- **Network**, 7 inputs: how connected recent reviewers are, reviews that followed a friend's review, PageRank-weighted stars, friends' recent mood and how generous they usually are.

Each group enters as a whole, so it is judged on everything it contains, not on a few inputs chosen with hindsight. The network inputs are also built on a scrambled placebo network.

We compare with four baselines. The **12-month mean** is the reference for every skill score. The **long-run mean** asks if the level returns to a fixed value. The **seasonal naive** — the same month last year — asks if there is a yearly pattern. And **last month** asks if the latest month is enough.

Finally, the **noise floor**: the error that even a model knowing each category's true level could not avoid. It is 0.141 stars before COVID and 0.170 during COVID."

### Explained very simply
- **Inputs** are the clues we give the model. Think of a detective: "history" clues are past stars, "sentiment" clues are what people wrote, "network" clues are about friends.
- **The four baselines**, as simple guesses for next month:
  - *12-month mean*: "next month will be like the average of the last year".
  - *Long-run mean*: "like the average since 2010".
  - *Seasonal naive*: "like the same month last year".
  - *Last month*: "like this month".
- **Skill**: the percentage by which a model's squared error is smaller than the 12-month mean's. Positive = better than the baseline.
- **Noise floor**: imagine you knew the *true* quality of a category perfectly. The monthly average would still wobble, because each month different people come and write. That wobble can never be predicted. 0.141 stars is the smallest error possible before COVID.
- **Placebo network**: we shuffle who is who in the friendship network, keeping everyone's number of friends. Same shape, wrong people. If it works as well as the real network, the real friendships carry no special information.
- **Global model**: one model learns from all 12 categories at once, so it has more data than 12 separate models.

---

## Slide 20 — Diagnostics of the series

### What to say
"Before forecasting, we looked closely at each category's monthly series from 2012 to 2017.

**Level plus noise.** Restaurants drift up slowly — trend strength 0.88, from about 3.75 to 4.05 stars. The other series hardly drift, and the season is weak.

**Mostly sampling noise.** Between 75 % and 151 % of the month-to-month changes is just the noise of averaging a limited number of reviews. And the correlation between one month's change and the next is −0.47, very close to the −0.50 we expect from pure noise around a level: a good month is followed by an ordinary one.

**Categories move alone.** Changes are correlated only 0.01 between categories; the one exception is 'Other' with Restaurants, at 0.6.

So each series is a slowly moving level plus noise. A good forecast must average many months, which is exactly what the 12-month mean does."

### Explained very simply
The figure has four panels:
- **(a) Mean stars per category** (smoothed over 6 months): mostly flat lines, Restaurants slowly going up.
- **(b) Noise share**: for each category, how much of the monthly change is just random wobbling. Around 100 % for most. Above 100 % happens because our noise formula slightly overestimates the noise (it treats each month's reviews as a totally random sample, but the mix of businesses changes little).
- **(c) Autocorrelation of monthly changes**: "if this month went up, does next month go down?" At lag 1 it is −0.47. Here is why that means noise: if the true level stays the same, a lucky high month is followed by a normal month, so up is followed by down. Pure noise gives exactly −0.50.
- **(d) Correlation between categories**: almost all near 0 — when Restaurants have a good month, Hotels don't. No shared shocks to learn from.

Other tests in the notebook:
- **STL** splits a series into trend + season + rest. **Trend strength** and **seasonal strength** go from 0 (none) to 1 (everything). Season is weak: at most 0.42.
- **ADF and KPSS** are two tests for "is the series stable around a level (stationary), or does it wander?" ADF says "stable" for 10 of 12 categories, but KPSS says "wanders" for 9 of them: the typical sign of a series that is *mostly* stable but drifts slowly. The monthly *changes* are stable for every category.

**What this means**: there is little to predict from one month to the next. The best you can do is average many months to cancel the noise.

---

## Slide 21 — Baselines and ARIMA

### What to say
"Here are the results of the baselines and the ARIMA family, before and during COVID. The dashed line is the noise floor.

**Averaging is essential.** The 12-month mean is the best baseline: 0.161 stars before COVID and 0.234 during. 'Last month' is much worse, 0.209 and 0.281, and the seasonal naive is worst, 0.217 and 0.321 — there is no usable yearly pattern.

**ARIMA equals the 12-month mean.** ARIMA(0,1,1) gets 0.162 against 0.161 before COVID and 0.232 against 0.234 during COVID; neither is significantly better. That makes sense: its moving-average coefficient is close to −1, so it also forecasts a long average. Compared with 'last month', it cuts the squared error by 40 % before COVID and 32 % during COVID. Adding a seasonal term changes the error by less than 1 %.

**Extra inputs make it worse.** ARIMAX with sentiment is 6.3 % worse before COVID, and that is significant (p = 0.009). ARIMAX with network inputs is slightly worse too. This is also the direct test of the warning idea from the gap slide: the recent change in the gap does not announce lower stars."

### Explained very simply
- **ARIMA** is a classic forecasting method for one series at a time. The three numbers (0,1,1) mean:
  - **0**: no "autoregressive" part (it does not copy last month directly).
  - **1**: it looks at the *change* from month to month instead of the level.
  - **1**: one "moving-average" term, which smooths out surprises.
  Together, ARIMA(0,1,1) is the same as **exponential smoothing**: a weighted average of the past, with more weight on recent months. When its coefficient is close to −1, it barely reacts to new months and becomes almost a long average — like our 12-month mean.
- **SARIMA** adds a seasonal piece: "this month is a bit like the same month last year". It didn't help.
- **ARIMAX** adds outside clues (the "X"):
  - *+ sentiment*: `sent_dev3` and `gap_dev3`, the recent change in text sentiment and in the gap.
  - *+ network*: `net_friend_mood` (the recent mood of the reviewers' friends) and `net_exposed_dev_3m` (how reviews that followed a friend's review deviated).
  - These two were chosen *in advance*, because ARIMA with 70–120 months can't handle dozens of inputs.
  - Inputs are taken from the previous month, so no future information is used.
- **Why worse with inputs?** The inputs mostly add random wiggles. The model tries to follow them and ends up further from the truth. It's like listening to gossip when the plain average was already right.
- **How we test** (Diebold-Mariano, pooled): for each month we average the difference in squared error over the 12 categories, then check if the average over 48 months is really different from zero, allowing for months being related to each other (Newey-West). Averaging within a month handles the fact that all categories share the same month.

### The numbers on the slide
| Model | Before COVID | During COVID |
|---|---|---|
| 12-month mean | 0.161 | 0.234 |
| ARIMA(0,1,1) | 0.162 | 0.232 |
| ARIMAX + network | 0.164 | 0.234 |
| ARIMAX + sentiment | 0.167 | 0.234 |
| Long-run mean | 0.189 | 0.265 |
| Last month | 0.209 | 0.281 |
| Seasonal naive | 0.217 | 0.321 |

(All are RMSE in stars; smaller is better. Noise floor: 0.141 and 0.170.)

---

## Slide 22 — Global models: LightGBM and Ridge

### What to say
"Instead of one model per category, a **global model** learns from all 12 categories together. We used two: **Ridge regression**, which is linear and careful, and **LightGBM**, gradient-boosted trees that can capture nonlinear patterns. Both predict the *difference* from the 12-month mean, so a model that finds nothing simply falls back to the baseline. We chose their settings on 2014–2017. Then each was fitted with history alone, plus sentiment, plus network, all three, and with the placebo network.

**Gains are small and unreliable.** The best models are 2 to 4 % better in squared error than the 12-month mean, but not significantly (p of 0.09 or more), and on the 2014–2017 validation years they were *not* better at all.

**Sentiment adds nothing.** For LightGBM it makes the error 1.1 % and 0.6 % worse, not significant; for Ridge 0.8 % and 0.7 % better, also not significant.

**The network is no better than a scrambled one.** The placebo network did as well, and before COVID it even had the lowest error of all, 0.1586. Comparisons of real against placebo have p-values from 0.47 to 0.93.

In the chart, every dot sits just left of the orange 12-month-mean line, and far from the noise floor on the left. The models barely move away from the baseline."

### Explained very simply
- **Ridge regression**: a straight-line model ("each clue adds or removes a fixed amount") with a brake that stops it from trusting any clue too much. Our tuning chose the strongest brake (alpha = 1,000): the data liked models that stay close to the baseline.
- **LightGBM**: many small decision trees ("if the trend is up and volume is high, then…") added together, each fixing the mistakes of the previous ones. The chosen settings are small and slow-learning (500 trees, learning rate 0.01, 4 leaves).
- **"Predict the difference from the 12-month mean"**: the model starts from the simple guess and only adds a correction. If it learns nothing, the correction is 0 and it gives the simple guess.
- **Validation years**: on 2014–2017 the best LightGBM had RMSE 0.1814, slightly *worse* than the 12-month mean (0.1790). So the small gains in 2018–2021 are best read as luck.
- **The chart**: each dot is one LightGBM version; the orange line is the 12-month mean; the dashed line is the noise floor. All dots are close to the orange line. The grey dot is the placebo, and it is as good as the real network (even slightly better before COVID).
- **"Not better than a scrambled one"**: if fake friends work as well as real friends, then the real friendship information is not what helps.
- **Many tests**: 18 comparisons were made. With that many, 1 or 2 small p-values appear by luck. The two "significant" ones were not network gains (one even showed the network making things worse).

### If someone asks
- *"Why is the placebo the best before COVID?"* — Its difference from the others is tiny and not significant. LightGBM has some randomness inside, so differences this small are luck.

---

## Slide 23 — Section 5: Integration

### What to say
"Now the core of the project: does the friendship network improve the forecasts, for categories and for single people?"

---

## Slide 24 — Does the network help?

### What to say
"Two safeguards keep this test honest. First, the pooled Diebold-Mariano test, which compares errors month by month while accounting for correlation. Second, the placebo network: the same inputs on a scrambled friendship graph.

**The inputs are not broken.** We checked each network input on the years before the test: no input correlates more than 0.07 with next month's surprise, and the placebo inputs correlate just as much. So the null result is not a coding bug; there is just no signal.

**The channel is too thin.** We can estimate how big a network effect could be at all. From the influence test, a friend's recent rating pulls you by 0.37. But only 1.3 % of reviews follow a friend's review within 30 days. And a friend's deviation is typically about 1 star. Multiply: 0.37 × 1.3 % × 1.00 ≈ **0.005 stars**.

**Noise hides it.** The sampling noise of a category's monthly mean is about 0.16 stars on average, and still 0.03 stars even for Restaurants, the biggest category. 0.005 is far too small to see."

### Explained very simply
- **Next month's surprise** = next month's stars minus the 12-month mean. It is the part a model must predict to beat the baseline.
- **Checking the inputs**: with 720 rows, a correlation must be bigger than about 0.07 to be different from 0. None is. The fake inputs behave the same.
- **The back-of-the-envelope calculation**, like a recipe:
  - How strongly a recent friend pulls you: 0.37 (from slide 17).
  - How often that happens: only 1.3 out of every 100 reviews have a friend's review of the same place in the 30 days before.
  - How big a friend's push usually is: about 1 star.
  - 0.37 × 0.013 × 1.00 ≈ 0.005 stars.
- **The bar chart**: the orange bar (0.005) is the network's possible effect. The blue bars are the noise: 0.033 for Restaurants and 0.156 for an average category. It's like trying to hear a whisper at a rock concert.
- **The lesson**: the effect is real for a person, but it is too rare to move an average over hundreds of reviews.

---

## Slide 25 — Where the network does help

### What to say
"If the network is too thin to move a category's average, maybe it shows for **one person**. We take reviews where a friend reviewed the same business before. We train on reviews before 2018 and test on 2018–2019.

With only the business's average, the error is 1.030 stars. Adding the user's usual generosity brings it to 1.001. Adding the friend's rating brings it to 0.987 overall — and when the friend reviewed less than 30 days earlier, from **0.946 to 0.864**, about 9 % lower. A matched stranger's rating changes almost nothing: 0.942.

This is **beyond chance**: with confidence intervals from resampling users, the friend's rating lowers the squared error by 0.028, with a 95 % range from −0.040 to −0.016, while the placebo changes it by only 0.0001.

The **cause is unknown**: it can be influence, joint visits or shared taste. And because the friend list has no dates, the gain may be a bit optimistic."

### Explained very simply
- **The task**: guess the stars one person will give to one business.
- **The chart**: each row adds one more clue. Blue dots are all friend pairs; orange dots are pairs where the friend wrote less than 30 days before.
  - *Business mean*: "you'll give what this place usually gets" → 1.030 (orange: 0.957).
  - *+ user generosity*: "and you are usually a bit generous or strict" → 1.001 (0.946).
  - *+ stranger's rating (placebo)*: "and a random other person rated it so" → 1.001 (0.942). Almost no help.
  - *+ friend's rating*: "and your friend rated it so" → 0.987 (0.864). Clear help, especially when the friend was recent.
- **Bootstrap over users**: we redo the calculation many times, each time picking users at random with replacement, to see how much the result could wobble. The whole range for the friend stays below 0 (always an improvement); the range for the stranger crosses 0 (no improvement).
- **Why the cause is unknown**: maybe you were influenced, maybe you went together, maybe you just like the same things. The data can't fully separate these.
- **Why it might be optimistic**: the friend list is from the end of the data. Some friendships may have started *after* the reviews, so we might be counting people who weren't friends yet.

### The numbers on the slide
- 0.946 → 0.864 RMSE when a friend reviewed within 30 days (9 % lower)
- Stranger: 0.942 (almost no change)
- MSE change: friend −0.028 (CI −0.040 to −0.016); placebo −0.0001
- 6,992 test reviews

---

## Slide 26 — Section 6: Conclusions

### What to say
"Finally: the answer to our main question, its limits, and what to do next."

---

## Slide 27 — Conclusions

### What to say
"Our answer: the monthly stars of a business category are **a slowly moving level plus noise**. The 12-month mean is already close to the best possible forecast, and neither the review text nor the reviewers' network improves it reliably.

- **Can next-month stars be forecast?** About as well as a 12-month average. The best models are 2 to 4 % better, but not significantly. Strong evidence: 48 months and 12 categories, walk-forward.
- **Does sentiment help?** No. ARIMAX with sentiment is 6.3 % worse before COVID. A strong null at this level.
- **Does the network help the category forecast?** No: not better than history alone, and not better than a scrambled network. A strong null, and we can explain why: the effect is about 0.005 stars against much larger noise.
- **Do friends predict one person's rating?** Yes: 0.946 to 0.864 when the friend's review is recent, and strangers add nothing. Strong for prediction, though the cause is unknown.
- **What explains ratings?** Service, above all, in every category.

In practice: to monitor a category, a 12-month average is hard to beat, and month-to-month movements are mostly noise. For a platform, a friend's recent rating is useful to predict what one person will think, but not to forecast averages. And for the study of social influence: an effect can be strong for individuals and invisible in aggregates when few people are exposed."

### Explained very simply
- The big picture: averages of many reviews move slowly and wobble randomly. The simple 12-month average already catches the slow movement; the wobble can't be predicted by anyone.
- Words and friends do contain information — friends clearly matter for a single person — but that information is too small or too rare to change an average of hundreds of reviews.
- "Strong null" means: we looked carefully, with fair tests and controls, and found nothing. That is a real result, not a failure.

---

## Slide 28 — Limitations and next steps

### What to say
"Our study has limits.
- Only 12 series and 48 test months: we have limited power to detect effects of 1 or 2 %.
- Friendship is an undated snapshot, and friends outside the area are missing.
- Influence is not identified: homophily and joint visits remain possible.
- The transformers ran on samples without fine-tuning, and VADER is weak for wait and price.
- 'California' is really the Santa Barbara area, with many visitors, so results may not transfer elsewhere.

Next steps:
- Forecast at an in-between level, like category by city, or clusters of similar businesses: more data than one business, more variation than a whole category.
- Fine-tune a transformer on Yelp with a GPU.
- Look for quasi-random exposure, for a more causal test — for example, a friend's review that happened to appear at the top of a page.
- Collect dated friendship ties, so the network inputs are completely free of look-ahead."

### Explained very simply
- **Limited power**: with only 12 series and 48 months, a small real improvement (1–2 %) could hide in the noise. Our test is like a magnifying glass that is not strong enough for the tiniest things.
- **Undated snapshot**: we know *who* is friends, but not *since when*.
- **Influence not identified**: we can't run an experiment where we force some people to read their friend's review. Without that, we can't be sure the friend *caused* the rating.
- **Quasi-random exposure**: a "natural experiment", where chance decides who sees a friend's review. That would make the test of influence much closer to cause and effect.
- **In-between level**: one business has too little data, one category averages away too much. Something in the middle might be the sweet spot.

---

## Slide 29 — Thank you / Questions

### What to say
"Thank you for listening. To summarise in one sentence: a category's monthly stars are a slowly moving level plus noise, and neither text nor network improves the 12-month average reliably — but a friend's recent rating does help to predict what one person will say. We are happy to take your questions."

### Possible questions from the audience, with simple answers

- **"Why did you choose the 12-month mean as the reference?"** — It was the best simple baseline, and the diagnostics showed that the series are a slowly moving level plus noise, which is exactly what a long average captures.

- **"Isn't a null result disappointing?"** — No. We tested carefully, with fair time splits, placebos and a calculation that explains *why* the network can't show up in averages. Knowing that something does not help, and why, is useful.

- **"Why does the network help for one person but not for a category?"** — For one person, a recent friend's review is a strong clue (0.37). But only 1.3 % of reviews have such a clue. In an average of hundreds of reviews, that tiny share moves the mean by about 0.005 stars, far below the noise.

- **"What is the placebo network exactly?"** — The same friendship network with user identities shuffled. Everyone keeps the same number of friends, but the friends are the wrong people. A real network effect must beat it.

- **"Why didn't you fine-tune the transformers?"** — Fine-tuning needs a GPU; we only had a CPU. That is why the comparison in slide 9 is "domain-trained simple model vs. off-the-shelf models", not a fair contest between model types.

- **"Could COVID explain everything?"** — We report results before and during COVID separately. The conclusions are the same in both periods; during COVID, errors are larger for every model.

- **"Is the sentiment useless then?"** — Not in general. It describes very well *why* people are happy or unhappy (service matters most). It just does not help to forecast a category's average next month.

- **"Why do you say service matters most if the regression is not causal?"** — We say "goes with", not "causes". Service sentiment has the strongest association with stars in every category.

---

## Small things to fix in the slides before presenting

- Slides 1 and 29: replace "[Author names and student numbers]" and "[Author names]".
- Slide 15: "With 100k to 471k edges" — in the notebook the smallest opinion row (service sentiment) uses 88k edges, so "88k to 471k" would match exactly.
