First registration
==================

We propose to use book reviews to pose questions about genre.

In the long run, we are interested in a number of historical questions. Are the categories used by contemporary observers better at characterizing fiction than those assigned retrospectively? Why do new genre categories emerge, and why do old ones disappear? Are genre categories perceptible first in literary production (as a cluster of similarities between books) or in literary reception (as a cluster of similarities between reviews)? Do genre categories generally become more crisply defined or more closely knit as we move from the late 19c, through the 20c, to the present?

But the immediate goal of this first registration is simply to ask whether the verbal similarity between fictional texts does (or does not) map onto verbal similarity between reviews, and to test different methods of measuring that similarity.

We may cast a bit of light on the question of whether genre categories are becoming more closely knit, but our conclusions there will be qualified by the fact that (for this first experiment) we are using genre categories assigned by late-20c librarians. At a later stage of the experiment, we will also test categories defined by contemporary observers.

Initial experiment: random pairs
-----------------------------------

We will randomly select 1,000 pairs of books published within 1 year of each other (so we're not just measuring language change) and not by the same author (so we're not measuring authorship). We'll measure the Word Mover's Distance between book texts and review texts to see whether book-distance correlates with review-distance.

Word Mover's Distance is a relatively recent distance metric that leverages word embeddings (word2vec or GloVe) to address the problem of synonymy. It is defined by [Kusner et al.](http://proceedings.mlr.press/v37/kusnerb15.pdf); our implementation will probably be based on [Mohit Rathore's gensim tutorial](https://markroxor.github.io/gensim/static/notebooks/WMD_tutorial.html).  Briefly, WMD uses word embeddings to calculate "distances" between words and then finds the shortest transport distance to transform the probability mass in doc1 to the closest corresponding mass in doc2. A standard list of English-language stopwords is removed from both documents before their probability distribution is calculated.

Our hypothesis is that there will be a (small) positive correlation between the distances measured between reviews and the distances measured between book texts. The sample will be large enough that we ought to see p < .05 in a one-tailed test if there is in reality any relationship.

Second experiment: pairs within the same metadata category
-------------------------------------------------------------

We'll select pairs of books that share a specific genre or subject heading (but not an author), and measure the WMD distance between their reviews and book texts. As a control, we will compare a randomly selected pair of books from the same two years. (So, for instance, if we were comparing a "Love story" from 1890 to a "Love story" from 1920, we would randomly select a control pair of books from 1890 and from 1920.) We will sort categories by the difference (mean-random-distance - mean-ingroup-distance), a quantity we'll call "review similarity" or "book similarity," depending on whether it's measured in reviews or in book texts.

Our hypothesis is a) that similarities measured in reviews and in book texts will correlate positively with each other across the [87 genre/subject categories with 9 or more examples.](https://github.com/tedunderwood/reviews/blob/master/bpo/corexperiment/meta/common_book_genres.tsv) We will also test b) whether this correlation holds even if we limit the list of categories to a set of 25 "form and genre categories" specified in [genre_categories_for_exp2.tsv.](https://github.com/tedunderwood/reviews/blob/master/bpo/corexperiment/meta/genre_categories_for_exp2.tsv)

But since 25 data points may not be enough to be confident about significance, here are some descriptive hypotheses: We expect that large categories ("novel", "romance", "historical fiction") will have weaker similarity (in both reviews and books) than smaller categories ("mystery", "western", "war stories").

We also expect that, across all categories, review similarity and book similarity will display a weak positive trend across time. (We believe genres are generally consolidating.) We will measure this by directly subtracting each control pair from each in-genre pair and aggregating the differences by year (the average of the two years being compared).

Finally, we are interested in identifying outliers on either side of the central trend line--categories that are *more* closely bound in reviews than in book-texts, or vice-versa. We will graph our 25 form and genre categories on a two-dimensional plane where review similarity is one axis and book similarity is the other.

Third experiment: predictive modeling of categories
------------------------------------------------------

We have used subject and genre headings assigned by librarians to create 14 categories (each containing more than 100 books). We hypothesize that categories more clearly marked in reviews (described using distinctive language) will also be clearly marked in the fictional texts themselves. We will test this hypothesis by comparing the predictive accuracy of models trained on groups of book-texts to the accuracy of models trained on reviews *of those same books*.

In each case, we will train a binary model distinguishing a positive category (e.g. "folklore") from a contrast set that excludes the positive category (e.g. "folklore_contrast"). Our broad hypothesis is that the accuracy of review-models will correlate positively with the accuracy of book-models, across a set of fourteen categories.

Models will use logistic regression. Features will be sorted using mutual information, and models will be optimized on the training/test set using grid search across the top n features and regularization constant C. Accuracy will be tested on a separate validation set.

After freezing the optimization method, we will do 10 test/validation splits and take the mean accuracy. (This is a makeshift solution for the small size of our validation sets.)

Caveats about the third experiment:
-----------------------------------

1. First, we know this part of the experiment is under-powered. Predictive modeling needs a larger set of texts than pairwise distance measurement, and we only have a small sample of genre tags (generated by 20-21c librarians). So we can only create fourteen categories of > 100 texts for this initial exploration. 14 data points is small even for a one-tailed test. We are not necessarily expecting to see p < .05.

2. We know there is an admixture of 5-10% nonfiction in our collection, and we have not manually removed it for this experiment. It is probably distributed unequally across categories.

3. We acknowledge that not all of the categories we are testing in this part of the experiment would normally be called "genres." Some would probably be called subject categories (e.g. books about North America). A couple of the categories are "controls," selected more or less randomly.

This means that any conclusions we draw from this part of the experiment will not apply with great force specifically to reasoning about fictional genre. For that sort of conclusion, see the 25 categories in 2b above.

Descriptive hypotheses:
-----------------------

 1. We would expect relatively random categories ('random' and 'unmarked') to be harder to classify than generic categories like ('novel' and 'romance').

 2. We also expect loose generic categories (like 'novel' and 'romance') to be harder to classify than more specialized categories (like 'war stories' and 'folklore').

 3. We expect the relations in A and B to hold both in fictional texts and in reviews.

 As we assess the value of different methods, we'll be interested in the relative strength of the correlations (mean *r* values) in the "second" and "third" experiments above. Do pairwise similarities correlate with predictive accuracy? Is the book-review correlation stronger using one method or the other?

References
-----------

For WMD using word2vec, we should cite

1. Mohit Rathore, "Finding Similar Documents with Word2vec and WMD," https://markroxor.github.io/gensim/static/notebooks/WMD_tutorial.html

2. Ofir Pele and Michael Werman, A linear time histogram metric for improved SIFT matching, 2008.

3. Ofir Pele and Michael Werman, Fast and robust earth mover's distances, 2009.

4. Matt Kusner et al. From Embeddings To Document Distances, 2015.

5. Thomas Mikolov et al. Efficient Estimation of Word Representations in Vector Space, 2013.
