# Neo4j_experiences
Here we shall try to do some cool stuffs with Neo4j

## Dataset files Description
* small_grouped_data.csv contains all the movies with their properties(columns). To load them in your database, use the Cypher Query below:
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/Foutse/Neo4j_experiences/main/dataset_movie/small_grouped_data.csv' AS l FIELDTERMINATOR ',' CREATE(m:MovieLens{M_movieId:toInteger(l.movieId),
M_rating:apoc.convert.fromJsonList(l.rating),M_userId:apoc.convert.fromJsonList(l.userId),
M_timestamp:l.timestamp,M_title:l.title,M_summary:l.summary,M_year:toInteger(l.movie_year),M_genres:apoc.convert.fromJsonList(l.genres),
M_Avg_rating:toFloat(l.Avg_rating),M_unique_tags:apoc.convert.fromJsonList(l.unique_tags),
M_unique_Uid_tags:apoc.convert.fromJsonList(l.unique_Uid_tags),M_round_ratings:apoc.convert.fromJsonList(l.round_ratings),
M_rating_vector:apoc.convert.fromJsonList(l.rating_vector),M_round_ratingsup:apoc.convert.fromJsonList(l.round_ratingsup),
M_round_ratingsdown:apoc.convert.fromJsonList(l.round_ratingsdown),M_rating_vectorup:apoc.convert.fromJsonList(l.rating_vectorup),
M_rating_vectordown:apoc.convert.fromJsonList(l.rating_vectordown)}) RETURN count(m)
* genre_rel_all.csv contains the relationship between movies based on common genre. The weight represents the jaccard similarity of their genre list. You can load the relationship to your AuraDB as follows:
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/Foutse/Neo4j_experiences/main/dataset_movie/genre_rel_all.csv' AS l FIELDTERMINATOR ',' 
  MATCH (m1:MovieLens {M_movieId: toInteger(l.source)}), (m2:MovieLens {M_movieId: toInteger(l.target)})
   WHERE m1 <> m2 AND toFloat(l.weight) > 0
   MERGE (m1)-[j:COMMON_GENRES]-(m2)
   ON CREATE SET j.weight = toFloat(l.weight)
RETURN count(j) as total_relations
The same logic applies for the other relationship files.
* user_rel_all.csv Relationship based on common users who watched the movies
* usertag_rel_all.csv Relationship based on common users who tagged the movies
* summary_textSimBert_all.csv Relationship based on similarity between the text summary of the movies, fixed to a certain treshold
* rating_vectordown_rel_all.csv Relationship based on similarity between their rating vector
* movie_tag_rel_all.csv Relationship based on the common tags they have
