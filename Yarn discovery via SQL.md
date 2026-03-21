## Task:
## Tools: MS Access, SQL
## Source: ravelry_yarns database on GitHub https://github.com/awalsh17/ravelry_yarns/tree/main
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
