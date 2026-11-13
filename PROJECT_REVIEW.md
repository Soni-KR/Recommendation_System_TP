# BoardGameGeek recommender

Mourad Kraiem and Mohamed Oussama Zoghlami · ENSI

[Open the notebook](BoardGameGeek_Hybrid_Recommender_V2.ipynb)

The notebook recommends board games from a rating profile or up to three liked games. You can filter by player count, maximum duration, and complexity.

## Project phases

| Phase | What we did and why |
| --- | --- |
| 1. Choose the data | Joined BoardGameGeek ratings to descriptions and game attributes by game ID. The source had 18,964,807 rating rows and metadata for 21,631 base games. Reading ratings in chunks kept memory use manageable. |
| 2. Prepare the data | Averaged duplicate ratings, removed invalid ratings and sparse users or games, and kept two hidden likes per evaluation user. The core has 324,348 ratings from 4,365 users on 4,709 games. The rating matrix is 98.42% empty. |
| 3. Build recommenders | Compared popularity, User–User, Item–Item, SVD, and TF-IDF. User–User led these warm baselines with 0.1379 NDCG@10. Content and game attributes support recommendations when rating history is limited. |
| 4. Combine scores | Mixed collaborative and content scores using validation. The warm demo uses 90% EASE and 10% TF-IDF. The cold experiments average scores for the liked games and test a popularity adjustment. |
| 5. Evaluate | Ranked eligible games and tracked how often hidden likes appeared, how high they ranked, and how much of the catalog appeared. Warm and cold results use different cohorts and candidate sets. |
| 6. Demonstrate | The notebook accepts an existing profile or one to three game titles or IDs. The demo applies hard limits and gives a reason for each recommendation. |
| 7. Document AI assistance | Codex helped implement and organize the notebook and experiments. Keep the full project conversations and document the authors’ own edits, decisions, and corrections for submission. |

The notebook applies **Ch. 1** through personalized Top-N recommendations, **Ch. 2** through collaborative filtering, **Ch. 3** through TF-IDF, **Ch. 4** through game attributes and hard constraints, **Ch. 5** through score blending, **Ch. 6** through validation and test metrics, and **Ch. 7** through context and explanations.

## Main results

Each test used 500 users and 1,000 hidden likes. Hit rate is the share of users with at least one hit in their top 10. NDCG rewards higher placements. Coverage is the share of the catalog that appeared across the recommendations.

### Cold start

| Model | Hit Rate@10 | NDCG@10 | Coverage@10 | Likes found |
| --- | ---: | ---: | ---: | ---: |
| Popularity | 20.0% | 0.0623 | 0.06% | 110 |
| One game, hybrid | 16.2% | 0.0563 | 6.26% | 91 |
| One game, hybrid with popularity | 21.8% | 0.0726 | 2.63% | 117 |
| Three games, Item–Item | 26.4% | 0.0957 | 2.41% | 152 |
| Three games, hybrid | 25.6% | 0.0949 | 3.35% | 146 |
| Three games, hybrid with popularity | **27.2%** | **0.0970** | 1.24% | **152** |

The one-game and three-game versions ranked the same candidates against the same hidden likes. Validation selected a 20% popularity weight. Three inputs improved the hybrid with popularity by 0.0244 NDCG (paired 95% interval 0.0107–0.0389). At three inputs, Item–Item found the same number of likes and covered nearly twice as many games.

### Warm start

| Model on the fresh test | Hit Rate@10 | NDCG@10 | Likes found |
| --- | ---: | ---: | ---: |
| Popularity | 29.0% | 0.0998 | 159 |
| EASE, 50% of user buckets, original penalty | 53.4% | 0.2252 | 313 |
| EASE, 50% of user buckets, tuned penalty | 53.4% | **0.2257** | **314** |
| EASE, all eligible buckets, tuned penalty | 51.4% | 0.2227 | 306 |

Tuning at 50% found one more hidden like. The paired interval for the ranking change includes zero. Using all eligible buckets also showed no clear gain. The notebook demo uses the original EASE and TF-IDF blend.

## Run the notebook

1. Install dependencies with `python -m pip install -r requirements.txt` from this folder.
2. Download `games_detailed_info.csv` and `bgg-19m-reviews.csv` from [BoardGameGeek Reviews](https://www.kaggle.com/datasets/jvanelteren/boardgamegeek-reviews) and put both in `data/`. Open the notebook in Jupyter and run it from top to bottom.
3. In the demo, try **Pandemic**, **7 Wonders**, and **Carcassonne** with four players and a 60-minute limit.
