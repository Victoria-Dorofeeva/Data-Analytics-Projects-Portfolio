## Task: 
I love crocheting and for my new amigurumi project I want to use a cotton yarn of the size "Super Bulky". There are no such products available in my local stores, so I decided to search for them online. I decided to take the data from a database assembled by a GitHub user based on yarn data from Ravelry.com - the largest online crocheting and knitting support community.
## Source: 
[ravelry_yarns database](https://github.com/awalsh17/ravelry_yarns/tree/main) on GitHub
## Tools: 
MS Access, SQL
## Preparations
I download raw files of yarn.csv and yarn_fibers.csv. Both tables have id column, which will serve as a primary key when I join the tables, since I need the information from both of them.
After editing correct data types for the columns and removing rows containing NULL values, I decide that I want to look into yarns that have top rating of 5 as the average rating, they need to be of size "Super Bulky" and be at least 80% cotton.
## Code:
    SELECT
        name,
        permalink,
        rating_average,
        yarn_company_name,
        yarn_weight_name,
        percentage,
        fiber_type_name
    FROM
        Yarn
        INNER JOIN Yarn_fibers ON Yarn.id = Yarn_fibers.id
    WHERE
        rating_average = 5
        AND yarn_weight_name = "Super Bulky"
        AND percentage >= 80
        AND fiber_type_name = "Cotton"
    ORDER BY
        name;
## End result: 
I have ended up with 37 yarn names that fit my criteria. Now I can google these yarns using permalinks and select the one I like the most for my next project.
<img width="953" height="814" alt="image" src="https://github.com/user-attachments/assets/ff0b2183-0c67-4ebb-91f4-3ceb09f2e92c" />
