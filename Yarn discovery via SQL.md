## Task: I love crocheting and for my new amigurumi project I want to use a cotton yarn of the size "Super Bulky". There are not a lot of such products available in my local stores, so I decided to search for them online. I decided to take the data from a database assembled by a GitHub user based on yarn data from Ravelry.com - the largest online crocheting and knitting community.
## Source: [ravelry_yarns database](https://github.com/awalsh17/ravelry_yarns/tree/main) on GitHub
## Tools: MS Access, SQL
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
## End result: I have ended up with 37 yarn names that fit my criteria. Now I can google these yarns using permalinks and select the one I like the most for my next project.
