---
title: "About"
date: "2016-05-05T21:48:51-07:00"
---


# Inner Join, Outter Join, Left Join, Right Join, Row Bind, Column Bind in R

The merge operations is well explained in the Pandas documentation which can be found <a href="https://pandas.pydata.org/pandas-docs/stable/merging.html">here</a> . However, I could not find any documentation that is as good as  Pandas's for R. Hence, I have decided to blog about it. 

Let's say we have these two dataframes 




```R
#import libraries

library('data.table')
df1<-data.frame(Country=c('US','UK',"New Zealand"),Rank =c(1,2,3))
df2<-data.frame(Country=c('US','UK',"New Zealand"),Population =c(300,80,4))
```

Before jumping to merging these two data frames, we can consider adding one data frame to another. This procss is called binding in R, while it is called concatenating in Python. There are two types of binding: row binding (rbind function) and column binding (cbind function).

## Row Binding

The **rbind** function appends the *rows* of  a data frame to another data frame (hence the letter 'r' before 'bind'). However, for this process to work,  both data frames must have the same number of columns and the columns in both data frame must have the same names; otherwise, we will get the following error:


```R
# df1 and df2 have different column names

df3 <- rbind(df1,df2)
```

However, if we try to bind df1 to another data frame which has the same column names or  itself, we get the following result


```R
#rbind of data frames with the same coulmn names
rbind(df1,df1)


```

Column Binding

The **cbind** append a data frame with the columns of another data frame ('c' stands for column). This operation does not require the same column names. However, it requires that both data frames must have the same number of row, otherwise, we will get this error:


```R
cbind(df1,df2[1:2,])
```


    Error in data.frame(..., check.names = FALSE): arguments imply differing number of rows: 3, 2
    Traceback:


    1. cbind(df1, df2[1:2, ])

    2. cbind(deparse.level, ...)

    3. data.frame(..., check.names = FALSE)

    4. stop(gettextf("arguments imply differing number of rows: %s", 
     .     paste(unique(nrows), collapse = ", ")), domain = NA)


However, when the number of rows in both data frames is the same, here is the result


```R
cbind(df1,df2)
```


<table>
<thead><tr><th scope=col>Country</th><th scope=col>Rank</th><th scope=col>Country</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>US         </td><td>1          </td><td>US         </td><td>300        </td></tr>
	<tr><td>UK         </td><td>2          </td><td>UK         </td><td> 80        </td></tr>
	<tr><td>New Zealand</td><td>3          </td><td>New Zealand</td><td>  4        </td></tr>
</tbody>
</table>



## Summary of row and column binding in R

1- ** rbind ** appends the rows  of a data frame to another. It requires that both data frames have the same column names

2- **cbind** appends the columns of one data frame to another and it requres that both data frames have the same number of rows

Let's say that you want to bind two data frames which have either different column names or different row numbers, how can we do it? This is when merging operation comes to play. 


## Outter Join (full)

let's say we want to append the following rows of df1 to df2, we can only do it using outer join


```R
#only the fist and second rows of df2
df2[1:2,]
df1
```


<table>
<thead><tr><th scope=col>Country</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>US </td><td>300</td></tr>
	<tr><td>UK </td><td> 80</td></tr>
</tbody>
</table>




<table>
<thead><tr><th scope=col>Country</th><th scope=col>Rank</th></tr></thead>
<tbody>
	<tr><td>US         </td><td>1          </td></tr>
	<tr><td>UK         </td><td>2          </td></tr>
	<tr><td>New Zealand</td><td>3          </td></tr>
</tbody>
</table>




```R
merge(df1,df2[1:2,],all=T,by=NULL)


```


<table>
<thead><tr><th scope=col>Country.x</th><th scope=col>Rank</th><th scope=col>Country.y</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>US         </td><td>1          </td><td>US         </td><td>300        </td></tr>
	<tr><td>UK         </td><td>2          </td><td>US         </td><td>300        </td></tr>
	<tr><td>New Zealand</td><td>3          </td><td>US         </td><td>300        </td></tr>
	<tr><td>US         </td><td>1          </td><td>UK         </td><td> 80        </td></tr>
	<tr><td>UK         </td><td>2          </td><td>UK         </td><td> 80        </td></tr>
	<tr><td>New Zealand</td><td>3          </td><td>UK         </td><td> 80        </td></tr>
</tbody>
</table>



However, you can see that there is redundant information. If we are to use the 'country' column as a key or an idenitifer for both data frames, then, we elimnate this redundancy and have this neat output. 


```R
merge(df1,df2[1:2,],all=T, by='Country') # this is equilvant to merge(df1,df2[1:2,],all=T)


```


<table>
<thead><tr><th scope=col>Country</th><th scope=col>Rank</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>New Zealand</td><td>3          </td><td> NA        </td></tr>
	<tr><td>UK         </td><td>2          </td><td> 80        </td></tr>
	<tr><td>US         </td><td>1          </td><td>300        </td></tr>
</tbody>
</table>



The first value for the population of New Zealand is given 'NA' value since we only used only the first and second rows of df2 and these two rows don't have New Zealand.  

*In short, the outer join when used with a key, it shows the union of both data frames.*

## Inner Join

The inner join is the oppoiste of outer join since its output is the intersection of both data frames. 
The outer join operation replaces the unaviloabe values with 'NA' while the inner join does not include it at all.



```R
merge(df1,df2[1:2,],all=F, by='Country') # this equivlant to merge(df1,df2[1:2,]) since this is the default 

```


<table>
<thead><tr><th scope=col>Country</th><th scope=col>Rank</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>UK </td><td>2  </td><td> 80</td></tr>
	<tr><td>US </td><td>1  </td><td>300</td></tr>
</tbody>
</table>



## Summary for outer and inner join

1- The outer join outputs the union of two data frames

2- the inner join outputs the interesction of two data frames

3- The inner join is the default if no arugment other than the data frames is provided

4- if no column name(s) are provided in the 'by' arugment, then the column name(s) which are the same in both dataframes are to be used

## Left and Right Join

The left join is a type of outer join. However, it uses only the keys or identifiers from the left table only (first table in the arugment). Hence, it outputs the entire left table and replaces the missing values from the right table with 'NA' or ommit them if they do not corospond to any values in the key(s) column(s).   


```R
merge(df1,df2[1:2,],all.x=T, by='Country') # replaces the missing values with NA for the right table
merge(df2[1:2,],df1,all.x=T, by='Country') # ommit New Zealand entries since New Zealand does not exist 
                                            #in the country column of the left table  


```


<table>
<thead><tr><th scope=col>Country</th><th scope=col>Rank</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>New Zealand</td><td>3          </td><td> NA        </td></tr>
	<tr><td>UK         </td><td>2          </td><td> 80        </td></tr>
	<tr><td>US         </td><td>1          </td><td>300        </td></tr>
</tbody>
</table>




<table>
<thead><tr><th scope=col>Country</th><th scope=col>Population</th><th scope=col>Rank</th></tr></thead>
<tbody>
	<tr><td>UK </td><td> 80</td><td>2  </td></tr>
	<tr><td>US </td><td>300</td><td>1  </td></tr>
</tbody>
</table>



The right join is the same as the left join. The only difference is that we are using the key(s) or idintifier(s) from the right table instead of the left table. Here is the same code above but with the right join:


```R
merge(df1,df2[1:2,],all.y=T, by='Country') # ommit New Zealand entries since New Zealand does not exist 
                                            #in the country column of the right table 
merge(df2[1:2,],df1,all.y=T, by='Country')  # replaces the missing values with NA for the left table





```


<table>
<thead><tr><th scope=col>Country</th><th scope=col>Rank</th><th scope=col>Population</th></tr></thead>
<tbody>
	<tr><td>UK </td><td>2  </td><td> 80</td></tr>
	<tr><td>US </td><td>1  </td><td>300</td></tr>
</tbody>
</table>




<table>
<thead><tr><th scope=col>Country</th><th scope=col>Population</th><th scope=col>Rank</th></tr></thead>
<tbody>
	<tr><td>New Zealand</td><td> NA        </td><td>3          </td></tr>
	<tr><td>UK         </td><td> 80        </td><td>2          </td></tr>
	<tr><td>US         </td><td>300        </td><td>1          </td></tr>
</tbody>
</table>


