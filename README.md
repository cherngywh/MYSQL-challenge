# MYSQL-challenge

use sakila;

**1a. Display the first and last names of all actors from the table actor.**
```mysql
select first_name, last_name
from actor;
```
# first_name, last_name
ZERO, CAGE


**1b. Display the first and last name of each actor in a single column in upper case letters. Name the column Actor Name.**
```mysql
select concat(first_name, " ", last_name) as Actor_Name
from actor;
```
**2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?**
```mysql
select actor_id, first_name, last_name from actor 
where first_name = "Joe" ;
```
**2b. Find all actors whose last name contain the letters GEN.**
```mysql
select * from actor 
where last_name like "%GEN%";
```
**2c. Find all actors whose last names contain the letters LI. This time, order the rows by last name and first name, in that order.**
```mysql
select * from actor 
where last_name like "%LI%" 
order by last_name, first_name;
```
**2d. Using IN, display the country_id and country columns of the following countries: Afghanistan, Bangladesh, and China.**
```mysql
select country_id, country from country 
where country in ("Afghanistan", "Bangladesh", "China");
```
**3a. Add a middle_name column to the table actor. Position it between first_name and last_name.**
```mysql
alter table actor add middle_name VARCHAR(50) not null after first_name;
```
**3b. You realize that some of these actors have tremendously long last names Change the data type of the middle_name column to blobs.**
```mysql
alter table actor modify column middle_name BLOB;
```
**3c. Now delete the middle_name column.**
```mysql
alter table actor drop middle_name;
```
**4a. List the last names of actors, as well as how many actors have that last name.**
```mysql
select last_name, count(last_name) as number_of_actors from actor 
group by last_name;
```
**4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors.**
```mysql
select last_name, count(last_name) as number_of_actors from actor 
group by last_name having count(last_name) >= 2;
```
**4c. Oh, no! The actor HARPO WILLIAMS was accidentally entered in the actor table as GROUCHO WILLIAMS, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.**
```mysql
update actor set first_name = "HARPO" 
where first_name = "GROUCHO" and last_name = "WILLIAMS";
```
**5a. You cannot locate the schema of the address table. Which query would you use to re-create it?**
```mysql
show create table address;
```
**6a. Use JOIN to display the first and last names, as well as the address, of each staff member. Use the tables staff and address:**
```mysql
select s.first_name, s.last_name, a.address from staff s
join address a using (address_id);
```
**6b. Use JOIN to display the total amount rung up by each staff member in August of 2005. Use tables staff and payment.**
```mysql
select s.first_name, s.last_name, sum(amount) from payment p
join staff s
on s.staff_id = p.staff_id
where p.payment_date like  "2005-08%"
group by s.staff_id;
```
**6c. List each film and the number of actors who are listed for that film. Use tables film_actor and film. Use inner join.**
```mysql
select f.title, count(a.actor_id) as number_of_actors from film_actor a
inner join film f
on a.film_id = f.film_id
group by f.film_id;
```
**6d. How many copies of the film Hunchback Impossible exist in the inventory system?**
```mysql
select count(i.inventory_id) as copies_of_film from inventory i
join film f
on i.film_id = f.film_id
where f.title = "Hunchback Impossible";
```
**6e. Using the tables payment and customer and the JOIN command, list the total paid by each customer. List the customers alphabetically by last name:**
```mysql
select c.first_name, c.last_name, sum(p.amount) as total_payment from customer c
join payment p
on c.customer_id = p.customer_id
group by c.customer_id
order by c.last_name;
```
**7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters K and Q have also soared in popularity. Use subqueries to display the titles of movies starting with the letters K and Q whose language is English.**
```mysql
select f.title from film f
where f.language_id in
(
select language_id from language
where name = "English"
)
and f.title like "K%" or f.title like "Q%";
```
**7b. Use subqueries to display all actors who appear in the film Alone Trip.**
```mysql
select a.first_name, a.last_name from actor a
where actor_id in
(
select fa.actor_id from film_actor fa
where fa.film_id in
(
select f.film_id from film f
where f.title = "Alone Trip"
));
```
**7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.**
```mysql
select m.first_name, m.last_name, m.email from customer m
join address a using (address_id)
join city c on a.city_id = c.city_id
join country y on y.country_id = c.country_id
where country = "Canada";
```
**7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.**
```mysql
select title from film f, category c, film_category fc
where  f.film_id = fc.film_id
and fc.category_id = c.category_id
and c.name = "Family";
```
**7e. Display the most frequently rented movies in descending order.**
```mysql
select f.title, count(f.title) as rented_times from rental r
join inventory i using (inventory_id)
join film f using (film_id)
group by f.title
order by count(f.title) DESC;
```
**7f. Write a query to display how much business, in dollars, each store brought in.**
```mysql
select s.store_id, concat("$", format(sum(amount), 2)) as revenue from payment p
join staff s using (staff_id)
group by s.store_id;
```
**7g. Write a query to display for each store its store ID, city, and country.**
```mysql
select s.store_id, c.city, y.country from store s, city c, country y, address a
where s.address_id = a.address_id
and a.city_id = c.city_id
and c.country_id = y.country_id;
```
**7h. List the top five genres in gross revenue in descending order.**
```mysql
select c.name, sum(p.amount) as gross_revenue from payment p 
join rental r using (rental_id)
join inventory i using (inventory_id)
join film_category fc using (film_id)
join category c using (category_id)
group by c.name
order by sum(p.amount) DESC limit 5;
```
**8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.**
```mysql
create view top_5_genres as 
select c.name, sum(p.amount) as gross_revenue from payment p 
join rental r using (rental_id)
join inventory i using (inventory_id)
join film_category fc using (film_id)
join category c using (category_id)
group by c.name
order by sum(p.amount) DESC limit 5;
```
**8b. How would you display the view that you created in 8a?**
```mysql
select * from top_5_genres;
```
**8c. You find that you no longer need the view top_five_genres. Write a query to delete it.**
```mysql
drop view top_5_genres;
```
