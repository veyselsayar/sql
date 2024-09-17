Challenge for cleaning MySQL data 

The steps I followed;

-- 1. REMOVE DUPLICATES
-- 2. STANDARDIZE THE DATA
-- 3. NULL VALUES OR BLANK VALUES
-- 4. REMOVE ANY COLUMNS


-- 1. REMOVE DUPLICATES 



SELECT * FROM layoffs;

CREATE TABLE staging_layoffs
LIKE layoffs;

INSERT staging_layoffs
SELECT * 
FROM layoffs;

WITH duplicate_cte as(
SELECT *,ROW_NUMBER()  OVER(
partition by company, industry, total_laid_off, percentage_laid_off,"date"
) as row_num
FROM staging_layoffs
)
select * from duplicate_cte 
where row_num > 1;

CREATE TABLE `staging_layoffs2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

INSERT INTO staging_layoffs2
SELECT *,ROW_NUMBER()  OVER(
partition by company, industry, total_laid_off, percentage_laid_off,"date"
) as row_num
FROM staging_layoffs;

SELECT * FROM staging_layoffs2;

DELETE 
FROM staging_layoffs2
WHERE row_num > 1; 


-- 2. STANDARDIZE DATA 



SELECT company, TRIM(company) from staging_layoffs2;

SELECT distinct industry
from staging_layoffs2
ORDER BY 1;

SELECT * 
FROM staging_layoffs2
WHERE industry Like  "Crypto%";

UPDATE staging_layoffs2
SET industry = "Crypto"
where industry like "Crypto%";
 
 
SELECT DISTINCT location 
FROM staging_layoffs2
ORDER BY 1;  

SELECT `date`, STR_TO_DATE(`date`,'%m/%d/%y')
FROM staging_layoffs2;



-- 3. NULL VALUES OR BLANK VALUES


DELETE
FROM staging_layoffs2
where total_laid_off is NULL
AND  percentage_laid_off is NULL ;

SELECT * FROM staging_layoffs2
where total_laid_off is NULL
AND  percentage_laid_off is NULL ;

SELECT * FROM staging_layoffs2;




-- 4. REMOVE ANY COLUMNS



ALTER TABLE staging_layoffs2
DROP COLUMN row_num;

SELECT * FROM staging_layoffs2;










