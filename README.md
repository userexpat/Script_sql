# Script_sql
SELECT 
    a.owner AS account_manager,
    a.account_name,
    right(a.phone, 9) AS'Phone',
    a.account_email,
    d.owner AS deal_owner,
    MAX(d.closing_date) AS last_deal_date_crm,
    d.amount 
FROM
    roam_horizon_crm.crm_expat_full_deals AS d
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts AS a ON a.entity_id = d.`account_name.id`
WHERE
	d.stage = 'Closed Won'
    AND d.owner in ('Khady Niane',
'Salla Lo',
'Fall Macodou',
'Mame Diarra NDAW',
'Mouhamadou Mandiang',
'Mouhamed Doudou Diallo',
'Mamadou Lo',
'Cheikh THIOMBANE',
'Seynabou Toure',
'Yaye Nab Diop',
'Aichatou FALL') -- Mettez les nom des commerciaux de lequipe
    AND right(a.phone, 9) in (
'773153237',
'771832424',
'773871727',
'773905622') 
 -- Mettez les numéros de téléphone extraits de la requête précédante
GROUP BY 1,2,3,4,5


-------------------revu par moi 

SELECT 
    MAX(d.closing_date) AS last_deal_date_crm,
    a.owner AS account_manager,
    a.account_name,
    right(a.phone, 9) AS 'Phone',
    a.account_email,
    d.owner AS deal_owner,
    d.amount 
FROM
    roam_horizon_crm.crm_expat_full_deals AS d
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts AS a ON a.entity_id = d.`account_name.id`
WHERE
    d.stage = 'Closed Won'
    AND d.owner in ('Khady Niane',
'Salla Lo',
'Fall Macodou',
'Mame Diarra NDAW',
'Mouhamadou Mandiang',
'Mouhamed Doudou Diallo',
'Mamadou Lo',
'Cheikh THIOMBANE',
'Seynabou Toure',
'Yaye Nab Diop',
'Aichatou FALL') -- Mettez les nom des commerciaux de lequipe
    AND right(a.phone, 9) in (
'778408443',
'771870619',
'709391488',
'773566270',
'774697420',
'778012489',
'775513937',
'782966767',
'774669406',
'775441815',
'773805108',
'779492582',
'781858524',
'775474305',
'773464496',
'785955990',
'776346246'
) 
GROUP BY 2,3,4

--------------------------
SELECT
    MAX(d.closing_date) AS last_deal_date_crm,
    a.owner AS account_manager,
    a.account_name,
    right(a.phone, 9) AS 'Phone',
    a.account_email,
    d.owner AS deal_owner_if,
    u.full_name,
    d.amount
FROM
    roam_horizon_crm.crm_expat_full_deals  as d
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts as a on a.id = d.`account_name`
    INNER JOIN roam_horizon_crm.crm_expat_full_allusers AS u ON u.id = d.owner
WHERE
    d.stage = 'Closed Won'
 AND right(a.phone, 9) in()
 
GROUP BY 2,3,4 --4,5
ORDER BY 'Phone', last_deal_date_crm DESC;

--------------------liste des annonces autos avec leurs account manager----------------------
select distinct 
       a.phone,
       json_unquote(json_extract(l.properties, '$.make')) as Fabriquer,
       json_unquote(json_extract(l.properties, '$.price')) as prix,
       json_unquote(json_extract(l.properties, '$.title')) as titre,
       json_unquote( json_extract(l.properties, '$.condition')) as conditions,
       json_unquote(json_extract(l.properties, '$.buildyear')) as annee,
       json_unquote(json_extract(l.properties, '$.mileage')) as kilometrage,
       a.owner account_manager,
       a.account_name,
       a.account_email
from 
    listings l 
left outer join 
        users u on l.user_id = u.id
left outer join 
    roam_horizon_crm.crm_expat_full_accounts a on a.user_id = u.id
left outer join 
    roam_horizon_crm.crm_expat_full_deals d on a.id = d.`account_name`
where 
    l.listing_type_id = "listings.vehicles.cars"
    and l.created_at between '2022-10-01' and '2023-01-31'
    and l.domain_id = 5
    and l.listing_status_id = 1 





-------------------- liste des annonces motos avec leurs account manager -------------

select distinct 
       a.phone,
       json_unquote(json_extract(l.properties, '$.make')) as Fabriquer,
       json_unquote(json_extract(l.properties, '$.price')) as prix,
       json_unquote(json_extract(l.properties, '$.title')) as titre,
       json_unquote( json_extract(l.properties, '$.condition')) as conditions,
       json_unquote(json_extract(l.properties, '$.buildyear')) as annee,
       json_unquote(json_extract(l.properties, '$.mileage')) as kilometrage,
       #json_unquote(json_extract(properties, '$.delivery-details')) as detail,
       a.owner account_manager,
       a.account_name,
       a.account_email,
       h.credits
from
    listings l 
left outer join 
    users u on l.user_id = u.id
left outer join 
    roam_horizon_crm.crm_expat_full_accounts a on a.user_id = u.id
left outer join    
    roam_horizon_crm.crm_expat_full_deals d on a.id = d.`account_name`
left outer join 
    horizon.user_credits h on h.id=u.id
where 
    l.listing_type_id = "listings.vehicles.motorbikes"
    and  l.created_at between '2022-10-01' and '2023-01-31'
    and l.domain_id = 5
    and l.listing_status_id = 1 



-------------------- liste des annonces pieces détachées motos et auto avec leurs account manager ------------------

select distinct 
       a.phone,
       json_unquote(json_extract(l.properties, '$.price')) as prix,
       json_unquote(json_extract(l.properties, '$.title')) as titre,
       json_unquote( json_extract(l.properties, '$.condition')) as conditions,
       a.owner account_manager,
       a.account_name,
       a.account_email
from 
    listings l 
left outer join 
    users u on l.user_id = u.id
left outer join
    roam_horizon_crm.crm_expat_full_accounts a on a.user_id = u.id
left outer join 
    horizon.entity_histories h on l.id = h.entity_id
where 
    l.listing_type_id = "listings.car-parts"
and 
    l.created_at between '2022-10-01' and '2023-01-31'
and
    l.domain_id = 5
and 
    l.listing_status_id = 1 
and  
    h.entity_type = 'listing'
and
    h.status_to = 'active'





---------------------- liste des annonces pieces détachées motos et auto avec leurs account manager avec la colonne encripter ----------------

select distinct 
       RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
       RIGHT(horizon.aes_decryption(ua.email),100) AS email,
       json_unquote(json_extract(l.properties, '$.price')) as prix,
       json_unquote(json_extract(l.properties, '$.title')) as titre,
       json_unquote( json_extract(l.properties, '$.condition')) as conditions,
       a.owner account_manager,
       a.account_name,
       a.account_email
from 
    listings l 
left outer join 
    roam_horizon_crm.crm_expat_full_accounts a on a.user_id = l.user_id
left outer join 
    horizon.entity_histories h on l.id = h.entity_id
left outer join 
    horizon.users_analyst_reference AS ua ON ua.id = l.user_id
where 
    l.listing_type_id = "listings.car-parts"
    and   l.created_at between '2022-10-01' and '2023-01-31'
    and   l.domain_id = 5
    and   l.listing_status_id = 1 
    and   h.entity_type = 'listing'
    and   h.status_to = 'active'

--------------------- liste des annonces offre d'emploi avec leurs account manager avec la colonne cripte ----------------
use horizon; 
select distinct 
       RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
       RIGHT(horizon.aes_decryption(ua.email),100) AS email,
       u.id,
       u.firstname as Prenom_annonceur,
       u.lastname as nom_annonceur, 
       json_unquote(json_extract(l.properties, '$.price')) as Prix,
       json_unquote(json_extract(l.properties, '$.title')) as Titre
from 
    listings l
inner join 
    users u on l.user_id = u.id
left outer join 
    horizon.users_analyst_reference AS ua ON ua.id = u.id
left outer join 
    horizon.entity_histories h on l.id = h.entity_id
where l.category_id in (
                        select id
                        from categories
                        where listing_type_id in( 'listings.services','listing.generic') and domain_id ='5')
and l.is_paid = 1
and l.created_at between "20220101" and "20221231" 
and l.domain_id ='5'
and  h.entity_type = 'listing'
and  h.status_to = 'active'



------------offre d'emploi avec les deals owners -----------

use horizon; 
select distinct 
       RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
       RIGHT(horizon.aes_decryption(ua.email),100) AS email,
       u.id,
       u.firstname as Prenom_annonceur,
       u.lastname as nom_annonceur, 
       json_unquote(json_extract(l.properties, '$.price')) as Prix,
       json_unquote(json_extract(l.properties, '$.title')) as Titre,
       a.owner account_manager,
       a.account_name,
       a.account_email,
       listing_type_id
from 
    listings l
inner join 
    users u on l.user_id = u.id
left outer join 
    roam_horizon_crm.crm_expat_full_accounts a on a.user_id = l.user_id
left outer join 
    horizon.users_analyst_reference AS ua ON ua.id = u.id
left outer join 
    horizon.entity_histories h on l.id = h.entity_id
where l.category_id in (
                        select id
                        from categories
                        where listing_type_id in( 'listings.services','listings.generic') and domain_id ='5')
and l.is_paid = 1
and l.created_at between "20220101" and "20221231" 
and l.domain_id ='5'
and  h.entity_type = 'listing'
and  h.status_to = 'active'


------------------ customer cars avec au moins un achat de 30K ----------
SELECT   
    distinct(right(horizon.aes_decryption(u.mobile_number),9)) AS 'Numéro de téléphone',
     uc.user_id,
     oi.price,
     uc.credits,
     a.account_name,
     a.last_deal_date,
     a.owner account_owner
FROM horizon.order_items AS oi 
    INNER JOIN horizon.orders AS o ON o.id = oi.order_id 
    INNER JOIN horizon.users_analyst_reference AS u ON u.id = o.user_id 
    LEFT JOIN horizon.products AS p ON p.id = oi.product_id 
    INNER JOIN horizon.user_credits AS uc ON uc.user_id = u.id  
    INNER JOIN horizon.listings as l on l.user_id = u.id 
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts a on a.user_id = u.id
WHERE
    o.order_status_id = 3 
    AND o.updated_at between cast('2022-09-01' as date) AND cast('2023-01-31' as date)
    AND oi.product_id = 96  
    AND o.domain_id = 5   
    and l.listing_type_id = 'listings.vehicles.cars'
    AND uc.credits <= 5  
    AND uc.credit_type = 'bought'
    and oi.price>= 30000




------------------ Expansion Plan 2023 Thies-Mbour----------

select 
l.id as  listing_id,
user_id,
username,
r.title as region,
loc.title as ville,
l.created_at,
l.online_at,
l.price as prix,
json_unquote(json_extract(l.properties, '$.title')) as titre,
l.location_id,
ls.description,
l.price as prix,
lt.title as subcategory
from horizon.listings l 
left outer join horizon.users u on l.user_id = u.id
left outer join horizon.locations as loc on loc.id = l.location_id
left outer join horizon.regions r on r.id = loc.region_id
left outer join horizon.listing_statuses ls on l.listing_status_id = ls.id
left outer join horizon.listing_types lt on lt.id = l.listing_type_id
where l.domain_id = 5
and l.listing_type_id in ( "listings.vehicles.cars","listings.vehicles.motorbikes","listings.vehicles.boats","listings.vehicles.generic","listings.car.hire" 
,"listings.car.parts","listings.vehicles.vans-buses","listings.vehicles.trucks-pick-ups")
and r.id=112
and l.created_at between '2023-04-01' and '2023-06-30'





---------------------nbre de clients avec leurs crédits --------------
SELECT
    distinct(right(horizon.aes_decryption(u.mobile_number),9)) AS 'Numéro de téléphone',
     uc.user_id,
     oi.price,
     uc.credits,
     a.account_name,
     a.last_deal_date,
     a.owner account_owner
FROM horizon.order_items AS oi 
    INNER JOIN horizon.orders AS o ON o.id = oi.order_id 
    INNER JOIN horizon.users_analyst_reference AS u ON u.id = o.user_id 
    LEFT JOIN horizon.products AS p ON p.id = oi.product_id 
    INNER JOIN horizon.user_credits AS uc ON uc.user_id = u.id  
    INNER JOIN horizon.listings as l on l.user_id = u.id
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts a on a.user_id = u.id
WHERE
     a.last_deal_date <= '2022-08-01'
    and oi.product_id = 96 
    AND o.domain_id = 5   
    AND uc.credit_type = 'bought'  
   


   --------------------- nbre de clients d'un gestionnaire ----------------------

   SELECT
    distinct(right(horizon.aes_decryption(u.mobile_number),9)) AS 'Numéro de téléphone',
     uc.user_id,
     oi.price,
     uc.credits,
     a.account_name,
     a.last_deal_date,
     a.owner account_owner
FROM horizon.order_items AS oi 
    INNER JOIN horizon.orders AS o ON o.id = oi.order_id 
    INNER JOIN horizon.users_analyst_reference AS u ON u.id = o.user_id 
    LEFT JOIN horizon.products AS p ON p.id = oi.product_id 
    INNER JOIN horizon.user_credits AS uc ON uc.user_id = u.id  
    INNER JOIN horizon.listings as l on l.user_id = u.id
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts a on a.user_id = u.id
WHERE
    a.owner= 'Mouhamadou Mandiang'
    and oi.product_id = 96 
    AND o.domain_id = 5   
    AND uc.credit_type = 'bought'  





--------------------- les annonces qui expurent dans une semaine -------------------


SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
    (CASE
        WHEN JSON_EXTRACT(u.properties, "$.account_manager") IS NULL
        THEN '-'
        ELSE JSON_EXTRACT(u.properties, "$.account_manager")
    END) AS 'Account Manager',
    uc.credits AS Credits,
    DATE(uc.expires_on) AS 'Expires On'
FROM
    horizon.users AS u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
INNER JOIN
    horizon.user_credits uc ON uc.user_id = u.id
WHERE
    u.domain_id = 5
    AND uc.credits > 0
    AND uc.credit_type = 'bought'
    AND DATE(uc.expires_on) >= CURDATE()
    AND DATE(uc.expires_on) < CURDATE() + INTERVAL 1 WEEK;




-------------------- les clients dont les crédits ont deja expiréees -------------------

    SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
    (CASE
        WHEN JSON_EXTRACT(u.properties, "$.account_manager") IS NULL
        THEN '-'
        ELSE JSON_EXTRACT(u.properties, "$.account_manager")
    END) AS 'Account Manager',
    uc.credits AS Credits,
    DATE(uc.expires_on) AS 'Expires On'
FROM
    horizon.users AS u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
INNER JOIN
    horizon.user_credits uc ON uc.user_id = u.id
WHERE
    u.domain_id = 5
    AND uc.credits > 0
    AND uc.credit_type = 'bought'
    AND uc.expires_on < NOW()



------------ Les annonces qui ont eux un remontée programmée ---------------------

SELECT
    l.id AS listingID,
    JSON_EXTRACT(l.properties, "$.title") AS title,
    c.title AS Category,
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
    DATE(f.start_at) AS StartAt,
    DATE(f.end_at) AS EndAT
FROM
    horizon.listings l
INNER JOIN
    horizon.users AS u ON u.id = l.user_id
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
INNER JOIN
    horizon.user_credits uc ON uc.user_id = u.id
INNER JOIN
    horizon.categories c ON c.id = l.category_id
INNER JOIN
    horizon.featured_entities f ON f.entity_id = l.id AND f.entity_type = 'listing'
WHERE
    l.domain_id = 5
    AND c.domain_id = 5
    AND u.domain_id = 5
    AND f.domain_id = 5
    AND f.product_id = 124;



---------------------- pour avoir le user id de tous les travailleurs de expat ------------------------


select distinct 
u.username, 
ua.id 
from horizon.users u 
left join horizon.role_user ru on u.id = ru.user_id 
join horizon.users_analyst_reference AS ua ON ua.id = u.id
where 
 ru.role_id in (1,8,12) 
 and domain_id = 5



----------------------- nombre de listing par semaine ----------------------------

 USE horizon;
SELECT 
    COUNT(distinct( l.id)) AS Listings
FROM
    listings l
        INNER JOIN
    entity_histories e ON l.id = e.entity_id
        AND e.entity_type = 'listing'
WHERE
        l.domain_id = 5
        AND e.status_to = 'active'
        AND e.created_at between '2023-02-27' and  '2023-03-05'


-------------- Number of purchase from the app (janvier-fevrier) -----------------

USE horizon;
SELECT 
    COUNT(DISTINCT (o.id)) AS orders
FROM
    orders o
WHERE
    o.domain_id = 5
        AND o.user_device = 'app'
        AND o.order_status_id = 3 -- pour filtrer les achats payé
        AND o.payment_method_id=20 ---- pour avoir la metrhode paydunya qui contient pour l'instant que du credit
        AND o.created_at between '2023-01-01' and  '2023-02-28'


----------------------------------- Number of purchase from the app -------------------------


SELECT 
    SUM(p.amount) AS totalAmount,
    o.created_at
FROM
   horizon.payments p
INNER JOIN
    horizon.orders o ON p.order_id = o.id
WHERE
    p.payment_status_id = 4 -- complete
    AND p.domain_id = 5
    AND o.payment_method_id = 20
    AND o.order_status_id = 3
    AND o.domain_id = 5
    AND o.user_device = 'app'
    AND DATE(o.created_at) > '2022-12-31' AND DATE(o.created_at) <= '2023-08-31'
    group by month(o.created_at)



----------------------------------------- nbre d'achat dans app avec des details ---------------------

USE horizon;
SELECT 
   count(DISTINCT (o.id)) AS orders,
   o.id as code_comman,
   o.user_id,
   o.created_at,
   oi.price,
   uc.credits,
  (right(horizon.aes_decryption(u.mobile_number),9)) as phone
FROM
    orders o
INNER JOIN users_analyst_reference AS u ON u.id = o.user_id 
INNER JOIN order_items AS oi ON o.id = oi.order_id 
INNER JOIN horizon.user_credits AS uc ON uc.user_id = u.id 
WHERE
    o.domain_id = 5
        AND o.user_device = 'app'
        AND o.order_status_id = 3 -- pour filtrer les achats payé
        AND o.payment_method_id=20
        AND o.created_at between '2023-01-01' and  '2023-07-31'
    group by user_id




---------------------- Number of paid listings (fevrier) -----------------

USE horizon;
SELECT 
    COUNT(distinct( l.id)) AS Listings ------- on met le count a cause du entity histories qui peut contenir des doublons
FROM
    listings l
        INNER JOIN
    entity_histories e ON l.id = e.entity_id
        AND e.entity_type = 'listing'
WHERE
        l.domain_id = 5
        AND e.status_to = 'active'
        AND e.created_at between '2023-02-01' and  '2023-02-28'
        AND l.is_paid = 1





---------------------------  pour avoir la catégorie des clients ---------------------



SELECT
    ua.id,
    u.username AS 'Client',
    c.title,
    max(count(lt.title))as lt,
    horizon.aes_decryption(ua.email) AS 'Adresse Email',
    horizon.aes_decryption(ua.mobile_number) AS 'Numéro de téléphone'

FROM 
 horizon.users AS u 
INNER JOIN horizon.listings l ON l.user_id = u.id
INNER JOIN horizon.categories c ON c.id = l.category_id
INNER JOIN horizon.users_analyst_reference ua ON ua.id = u.id
INNER JOIN horizon.listing_types lt on lt.id = l.listing_type_id
WHERE right(horizon.aes_decryption(ua.mobile_number),9)
 in (
'771808051',
'781854404',
'778211148',
'764763710',
'771086911',
'764763710',
'771086911',
'764763710',
'771086911',
'770966622',
'784544506',
'774141650',
'784613283',
'773651047',
'776881163',
'777954918',
'777954918',
'777954918',
'777954918',
'779397051',
'761948365',
'784544506',
'784544506',
'778415633',
'773867957',
'778415633',
'773867957',
'778415633',
'778415633',
'778415633',
'778415633',
'778415633',
'778415633',
'778415633',
'778415633',
'778415633',
'771373433',
'777462691',
'771257777',
'771257777',
'771257777',
'771257777',
'771257777',
'778980111',
'774557481',
'775144338',
'773051028',
'773051028',
'778114305',
'778114305',
'784544506',
'774714932',
'776661568',
'778114305',
'767446109',
'781323131',
'773823030',
'753306182',
'772149603',
'772604436',
'781323131',
'785268888',
'709242412',
'775325817',
'777673989',
'777673989')


SELECT
    ua.id,
    u.username AS 'Client',
    c.title,
    count(lt.title)as lt,
    lt.title as lts,
    horizon.aes_decryption(ua.email) AS 'Adresse Email',
    horizon.aes_decryption(ua.mobile_number) AS 'Numéro de téléphone'

FROM 
 horizon.users AS u 
INNER JOIN horizon.listings l ON l.user_id = u.id
INNER JOIN horizon.categories c ON c.id = l.category_id
INNER JOIN horizon.users_analyst_reference ua ON ua.id = u.id
INNER JOIN horizon.listing_types lt on lt.id = l.listing_type_id
WHERE right(horizon.aes_decryption(ua.mobile_number),9)
 in (
'771808051',
'781854404',
'778211148',
'778855650')
group by 1,2,3,5,6,7;


---------------------------  Liste des annonces créées et annonces remontées ces 3 derniers mois pour location dans cars  -------------------------

SET @start_date = '2022-09-01';
SET @end_date = curdate();
    SELECT
            l.user_id as id,
            u.username as Cars_client,
            c.id AS CategoryID,
            c.title AS Category,
            p.title AS  option_visibilitée,
            cast(o.created_at as date) as date,
            COUNT(DISTINCT(o.id)) AS numb_options,
            RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
            RIGHT(horizon.aes_decryption(ua.email),100) AS email
        FROM
            horizon.orders o
        INNER JOIN
            horizon.order_items i ON i.order_id = o.id
        INNER JOIN
            horizon.listings l ON l.id = i.entity_id
        INNER JOIN
            horizon.categories c ON l.category_id = c.id
        INNER JOIN
            horizon.products p ON p.id = i.product_id
        INNER JOIN 
            horizon.users AS u ON u.id = l.user_id
        INNER JOIN 
            horizon.users_analyst_reference as ua ON ua.id = l.user_id
        WHERE
            o.domain_id = 5
            AND i.entity_type = 'listing'
            AND c.id = 1650
            AND o.order_status_id = 3
            AND DATE(o.created_at) >= @start_date
        GROUP BY 1,2,3,4,5,6,8,9



--------------------------  Liste des annonces créées et annonces remontées ces 3 derniers mois pour appartement meublés-------------------------

SET @start_date = '2022-09-01';
SET @end_date = curdate();
    SELECT
            l.user_id as id,
            u.username as Cars_client,
            c.id AS CategoryID,
            c.title AS Category,
            p.title AS  option_visibilitée,
            cast(o.created_at as date) as date,
            COUNT(DISTINCT(o.id)) AS numb_options,
            RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
            RIGHT(horizon.aes_decryption(ua.email),100) AS email
        FROM
            horizon.orders o
        INNER JOIN
            horizon.order_items i ON i.order_id = o.id
        INNER JOIN
            horizon.listings l ON l.id = i.entity_id
        INNER JOIN
            horizon.categories c ON l.category_id = c.id
        INNER JOIN
            horizon.products p ON p.id = i.product_id
        INNER JOIN 
            horizon.users AS u ON u.id = l.user_id
        INNER JOIN 
            horizon.users_analyst_reference as ua ON ua.id = l.user_id
        WHERE
            o.domain_id = 5
            AND i.entity_type = 'listing'
            AND c.id = 1664
            AND o.order_status_id = 3
            AND DATE(o.created_at) >= @start_date
        GROUP BY 1,2,3,4,5,6,8,9




----------------Assurance --------------------

SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
    RIGHT(horizon.aes_decryption(ua.email),100)
FROM
    horizon.users u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
    u.domain_id = 5
    AND (
        u.username LIKE '%assurance%'
        OR RIGHT(horizon.aes_decryption(ua.email),100) LIKE '%assurance%'
        OR u.firstname LIKE '%assurance%'
        OR u.lastname LIKE '%assurance%'
    )
    AND u.deleted_at IS NULL
ORDER BY u.id DESC;





use horizon; 
select distinct 
       RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone,
       RIGHT(horizon.aes_decryption(ua.email),100) AS email,
       u.id,
       u.firstname as Prenom_annonceur,
       u.lastname as nom_annonceur, 
       json_unquote(json_extract(l.properties, '$.price')) as Prix,
       json_unquote(json_extract(l.properties, '$.title')) as Titre,
       a.owner account_manager,
       a.account_name,
       a.account_email,
       listing_type_id,
       d.owner AS deal_owner,
      d.amount 
from 
    listings l
inner join 
    users u on l.user_id = u.id
left outer join 
    roam_horizon_crm.crm_expat_full_accounts a on a.user_id = l.user_id
INNER JOIN
    roam_horizon_crm.crm_expat_full_deals AS d ON a.entity_id = d.`account_name.id`
left outer join 
    horizon.users_analyst_reference AS ua ON ua.id = u.id
left outer join 
    horizon.entity_histories h on l.id = h.entity_id
where l.category_id in (
                        select id
                        from categories
                        where listing_type_id in( 'listings.services','listings.generic','listings.jobs') and domain_id ='5')
and l.is_paid = 1
and l.created_at between "20221001" and "20230331" 
and l.domain_id ='5'
and h.entity_type = 'listing'
and h.status_to = 'active'
and d.stage = 'Closed Won'


------------------------------- credits usage ou options de visibilité  -------------------------

SET @start_date = '2022-09-01';
SELECT
    l.user_id,
    c.id AS CategoryID,
    p.title AS title,
    CAST(o.created_at AS DATE) as date,
    c.title AS Category,
    COUNT(DISTINCT(o.id)) AS Totale
FROM
    horizon.orders o
INNER JOIN
    horizon.order_items i ON i.order_id = o.id
INNER JOIN
    horizon.listings l ON l.id = i.entity_id
INNER JOIN
    horizon.categories c ON l.category_id = c.id
INNER JOIN
    horizon.products p ON p.id = i.product_id
WHERE
    o.domain_id = 5
    AND i.entity_type = 'listing'
    AND o.order_status_id = 3
    AND CAST(o.created_at as date) >= @start_date
    --,j;and deleted_at is null
GROUP BY 1,2,3
ORDER BY date DESC;


--------------------------

SELECT
    l.id as listing_id,
    o.id as order_id,
    p.title AS product,
    DATE(o.created_at) AS date,
    p.credits,
    c.title as category,
    is_paid,
    l.user_id
FROM
    horizon.orders o
INNER JOIN
    horizon.order_items i ON i.order_id = o.id AND i.entity_type = 'listing'
INNER JOIN
    horizon.listings l ON l.id = i.entity_id
INNER JOIN
    horizon.products p ON p.id = i.product_id
INNER JOIN
    horizon.categories c ON l.category_id = c.id
WHERE
    o.domain_id = 5
    AND l.domain_id = 5
    AND p.domain_id = 5
    AND o.order_status_id = 3
    AND CAST(o.created_at as date) >= @start_date
GROUP BY 1,2,3
ORDER BY listing_id DESC;



------------------

SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone
FROM
    horizon.users u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
    u.domain_id = 5
    AND (
        u.username LIKE '%cbao%'
        OR u.email LIKE '%cbao%'
        OR u.firstname LIKE '%cbao%'
        OR OR u.lastname LIKE '%cbao%'
    )
    AND u.deleted_at IS NULL
ORDER BY u.id DESC;
--------------------------------------

SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone
FROM
    horizon.users u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
    u.domain_id = 5
    AND (
        u.username LIKE '%banque%'
        OR u.email LIKE '%banque%'
        OR u.firstname LIKE '%banque%'
        OR OR u.lastname LIKE '%banque%'
    )
    AND u.deleted_at IS NULL
ORDER BY u.id DESC;



SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone
FROM
    horizon.users u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
    u.domain_id = 5
    AND (
        u.username LIKE '%bhs%'
        OR u.email LIKE '%bhs%'
        OR u.firstname LIKE '%bhs%'
        OR OR u.lastname LIKE '%bhs%'
    )
    AND u.deleted_at IS NULL
ORDER BY u.id DESC;


---------------------

SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone
FROM
    horizon.users u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
    u.domain_id = 5
    AND (
        u.username LIKE '%bimao%'
        OR u.email LIKE '%bimao%'
        OR u.firstname LIKE '%bimao%'
        OR OR u.lastname LIKE '%bimao%'
    )
    AND u.deleted_at IS NULL
ORDER BY u.id DESC;

--------------------

SELECT
    u.id AS UserId,
    u.firstname AS Firstname,
    u.lastname AS Lastname,
    RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone
FROM
    horizon.users u
INNER JOIN
    horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
    u.domain_id = 5
    AND (
        u.username LIKE '%boa%'
        OR u.email LIKE '%boa%'
        OR u.firstname LIKE '%boa%'
        OR OR u.lastname LIKE '%boa%'
    )
    AND u.deleted_at IS NULL
ORDER BY u.id DESC;


---------------------------- Toute les annonces de autorent ----------------------------

SELECT distinct
    l.id,
    l.slug,
    json_unquote(json_extract(l.properties, '$.make')) as Fabriquer,
       json_unquote(json_extract(l.properties, '$.price')) as prix,
       json_unquote(json_extract(l.properties, '$.title')) as titre,
       json_unquote( json_extract(l.properties, '$.condition')) as conditions,
       json_unquote(json_extract(l.properties, '$.buildyear')) as annee,
       json_unquote(json_extract(l.properties, '$.mileage')) as kilometrage,
    l.created_at,
    l.updated_at,
    l.disable_at,
    e.transition,
    e.status_to,
    l.first_online_at
FROM
    horizon.listings l
INNER JOIN
    horizon.users u ON l.user_id = u.id
INNER JOIN
    horizon.user_credits uc ON uc.user_id = u.id
INNER JOIN
    horizon.entity_histories e ON l.id = e.entity_id AND e.entity_type = 'listing'
WHERE
    l.domain_id = 5
    AND u.domain_id = 5
    AND username = 'AUTORENT OCCASION'
   


--------------------- 


SELECT distinct
    l.id,
    l.slug,
    json_unquote(json_extract(l.properties, '$.make')) as Fabriquer,
       json_unquote(json_extract(l.properties, '$.price')) as prix,
       json_unquote(json_extract(l.properties, '$.title')) as titre,
       json_unquote( json_extract(l.properties, '$.condition')) as conditions,
       json_unquote(json_extract(l.properties, '$.buildyear')) as annee,
       json_unquote(json_extract(l.properties, '$.mileage')) as kilometrage,
       json_unquote(json_extract(l.properties, '$.transmission')) as transmission,
      json_unquote(json_extract(l.properties, '$.fuel')) as fuel,
    l.created_at,
    l.updated_at,
    l.disable_at,
    -- e.transition,
    -- e.status_to,
    l.first_online_at
FROM
    horizon.listings l
INNER JOIN
    horizon.users u ON l.user_id = u.id
-- INNER JOIN
-- horizon.entity_histories e ON l.id = e.entity_id AND e.entity_type = 'listing'
WHERE

     l.user_id = 1182028
     and listing_status_id = 1
    -- AND e.transition = 'approve'
   -- and l.is_business = 0 -- annonce 1showroom 
    and l.disable_at > '2023-04-29'


------------------- liste des clients de Amadou wade ------------------

SELECT
      u.id AS UserId,
      u.firstname AS Firstname,
      u.lastname AS Lastname,
      u.properties ->> '$."account_manager"' as 'Manager_ID',
      RIGHT(horizon.aes_decryption(ua.mobile_number),9) AS phone
FROM
      horizon.users u
INNER JOIN
      horizon.users_analyst_reference AS ua ON ua.id = u.id
WHERE
      u.domain_id = 5
      AND JSON_UNQUOTE(JSON_EXTRACT(u.properties, "$.account_manager")) = '1099969';
    
    




insert into Listings_DataMart.Listing_Fact(Id,User_id, Location_id,Online_at,Created_at,listing_type_id,is_paid,is_business,price)
select id,user_id, location_id,online_at,created_at,listing_type_id,is_paid,is_business,price from horizon.listings where id = '499925'



select l.id , l.user_id, l.location_id, l.listing_status_id, l.online_at, l.created_at, l.listing_type_id, l.is_paid, l.is_business, l.price,
(select d.title from categories d where id = (select parent_id from categories where id = l.category_id)) as category, 
(select title from categories where id = l.category_id) as sub_category
from listings l
where l.location_id is not null;


-------------------- nbre de listing par user_id ---------------

USE horizon;
SELECT 
    COUNT(DISTINCT (l.id)) AS Listings,
    user_id
FROM
    listings l
WHERE
    
        user_id in('1427090',
'1365696',
group by user_id


------------------------------------------ information sur les listings ----------------


USE horizon;
SELECT 
    l.id,l.user_id,l.user_device,l.created_at,l.listing_type_id,l.price,c.listing_type_id,c.title
FROM
    listings l
        INNER JOIN
    categories c ON l.category_id = c.id
      INNER JOIN
    entity_histories e ON l.id = e.entity_id
        AND e.entity_type = 'listing'
WHERE
 
        l.created_at between '2023-04-01' and  '2023-08-31' and l.domain_id = 5  and listing_status_id = 1 AND e.status_to = 'active'


----------------------------------------

SET @start_date = '2022-09-01';
SELECT
    l.user_id,
    -- c.id AS CategoryID,
    c.title AS Category,
    l.id as listing_id,
    -- is_paid,
    o.id as orders_id,
    p.title AS title,
    CAST(o.created_at AS DATE) as date,
    COUNT(DISTINCT(i.id)) AS Totale
FROM
    horizon.orders o
INNER JOIN
    horizon.order_items i ON i.order_id = o.id
INNER JOIN
    horizon.listings l ON l.id = i.entity_id
INNER JOIN
    horizon.categories c ON l.category_id = c.id
INNER JOIN
    horizon.products p ON p.id = i.product_id
WHERE
    o.domain_id = 5
    AND i.entity_type = 'listing'
    AND o.order_status_id = 3
    AND CAST(o.created_at as date) >= @start_date
    and deleted_at is null
GROUP BY 1,2,3,4,5
ORDER BY date DESC;




---------------------------------

SELECT
    l.id as listing_id,
    o.id as order_id,
    p.title AS product,
    DATE(l.created_at) AS date,
    p.credits,
    c.title as category,
    is_paid,
    l.user_id,
    l.price,
    py.amount
FROM
    horizon.listings l 
INNER JOIN
    horizon.orders o ON o.user_id = l.user_id
INNER JOIN
    horizon.order_items i ON i.order_id = o.id AND i.entity_type = 'listing'
INNER JOIN
    horizon.products p ON p.id = i.product_id
INNER JOIN
    horizon.categories c ON l.category_id = c.id
INNER JOIN
    horizon.payments py ON py.user_id = l.user_id
WHERE
    o.domain_id = 5
    AND l.domain_id = 5
    AND p.domain_id = 5
    AND o.order_status_id = 3
    AND l.created_at between '2022-08-01' AND '2023-07-31' 
GROUP BY 1,2,3
ORDER BY listing_id DESC;




-------------------------pour avoir le account name pour le script suivant ----------------

SELECT
    d.id as deal_id,
    d.account_name,
    a.modified_by as user_id,
    right(a.phone, 9) 
FROM
    roam_horizon_crm.crm_expat_full_deals  as d
    INNER JOIN roam_horizon_crm.crm_expat_full_accounts as a on a.id = d.`account_name`
WHERE
    d.stage = 'Closed Won'
 AND right(a.phone, 9) in('706301826',
'785336355',
'765730699',
'776576972',
'774786567',
'777328355',
'772612584',
'777988376',
'777007377',
'770399971',
'772992268',
'776374201',
'776452860',
'775267602',
'781355913',
'772953539',
'784354020',
'768520236',
'778544848',
'772635372',
'773360155',
'762848978',
'770936666',
'771171771',
'773613644',
'771563293',
'775162883',
'766377144',
'775540335',
'776765891',
'771410071',
'781836532',
'776161231',)








 ----------------------------- pour avoir le dernier sale qui a fait l'appel--------

 SELECT
d.id AS deal_id,
d.deal_name AS deal_name,
act.modified_by AS user_id,
right(a.phone, 9),
act.full_name AS full_name,
act.last_activity_date,
act.activity_type,
act.rn,
d.closing_date

FROM roam_horizon_crm.crm_expat_full_deals AS d
INNER JOIN roam_horizon_crm.crm_expat_full_accounts as a on a.id = d.`account_name`
LEFT JOIN (
    SELECT
    first_value(act.id) OVER (PARTITION BY act.what_id ORDER BY act.modified_time DESC) AS last_activity_id,
    act.owner,
    act.modified_by, 
    act.what_id,
    act.activity_type,
    first_value(act.modified_time) OVER (PARTITION BY act.what_id ORDER BY act.modified_time DESC) AS last_activity_date,
    u.full_name,
    ROW_NUMBER() OVER (PARTITION BY act.what_id ORDER BY act.modified_time DESC) AS rn

    FROM roam_horizon_crm.crm_expat_full_activities AS act
    INNER JOIN roam_horizon_crm.crm_expat_full_allusers AS u ON u.id = act.modified_by 
    WHERE 
    act.se_module = 'Accounts'
    -- AND act.what_id = 3928796000000420067

    -- WHERE id = 3928796000064304001

    ) AS act ON act.what_id = d.account_name
WHERE 
d.account_name in (
'3928796000000426796',
'3928796000000433551',
'3928796000000433589',
'3928796000000420187',
'3928796000004998186',
'3928796000000426682',
'3928796000002263077',
'3928796000005919079',
'3928796000000429199',
'3928796000004998468',
'3928796000004998125',
'3928796000000420166',
'3928796000000420639',
'3928796000000426796',
'3928796000000433133',
'3928796000000426430',
'3928796000000428683',
'3928796000000430068',
'3928796000000420067',
'3928796000000420874',
'3928796000000420060',)

and act.rn = 1
AND d.stage = 'Closed Won'

ORDER BY d.closing_date DESC;




---------------------------- Nbre de paying customer cars ---------------------------

SELECT 
        COUNT(DISTINCT l.user_id) AS user_count
    FROM 
        horizon.listings l 
    INNER JOIN 
        horizon.listing_statuses ls ON l.listing_status_id = ls.id
    INNER JOIN 
        horizon.categories ON l.category_id = categories.id
    LEFT JOIN 
        horizon.categories cat2 ON categories.parent_id = cat2.id
    INNER JOIN 
        horizon.listing_types lt ON l.listing_type_id = lt.id 
    WHERE 
        l.domain_id = 5
        AND l.created_at BETWEEN '2023-08-01' AND '2023-07-31'
        AND l.is_paid = 1
        AND ls.id IN ('1', '7')
        AND categories.title IN ('Citadines', '4x4s & SUV', 'Voitures', 'Berlines & breaks', 'Coupés & cabriolets', 'Véhicules', 'Pick-ups')
    GROUP BY l.user_id


----------------------------------- listing avec leurs categories --------------------------------

SELECT
    l.id as listing_id,
    l.user_id,
    DATE(l.created_at) AS date,
    l.online_at,
    l.price,
    ls.title,
    l.is_business,
    l.listing_type_id,
    lt.title,
    categories.title,
    cat2.title,
    l.deleted_at,
    is_paid
FROM
    horizon.listings l 
INNER JOIN 
    horizon.listing_statuses ls on l.listing_status_id = ls.id
inner join 
    horizon.categories ON l.category_id = categories.id
left join 
    horizon.categories cat2 ON categories.parent_id = cat2.id
INNER JOIN 
    horizon.listing_types lt on l.listing_type_id = lt.id 
WHERE
  l.domain_id = 5
  AND l.created_at between '2022-08-01'  AND '2023-07-31' 
GROUP BY 1,2
ORDER BY listing_id DESC;




---------------------------- creer une vue 

CREATE VIEW listing_by_category AS
SELECT
    l.id AS listing_id,
    l.user_id,
    DATE(l.created_at) AS date,
    l.online_at,
    l.price,
    ls.title,
    l.is_business,
    l.listing_type_id,
    lt.title AS listing_type_title,
    categories.title AS category_title,
    cat2.title AS parent_category_title,
    l.deleted_at,
    is_paid
FROM
    horizon.listings l 
INNER JOIN 
    horizon.listing_statuses ls ON l.listing_status_id = ls.id
INNER JOIN 
    horizon.categories ON l.category_id = categories.id
LEFT JOIN 
    horizon.categories cat2 ON categories.parent_id = cat2.id
INNER JOIN 
    horizon.listing_types lt ON l.listing_type_id = lt.id 
WHERE
    l.domain_id = 5
GROUP BY
    listing_id, user_id
ORDER BY
    listing_id DESC;


---------------------------------

use horizon;
CREATE VIEW listing_by_category AS
SELECT
    l.id AS listing_id,
    l.user_id,
    DATE(l.created_at) AS date,
    l.online_at,
    l.price,
    ls.title,
    l.is_business,
    l.listing_type_id,
    lt.title AS listing_type_title,
    categories.title AS category_title,
    cat2.title AS parent_category_title,
    CASE WHEN amadou.proper_case(amadou.clean_french(cat5.title)) = "Annonces" THEN amadou.proper_case(amadou.clean_french(cat4.title))
    WHEN amadou.proper_case(amadou.clean_french(cat4.title)) = "Annonces" THEN amadou.proper_case(amadou.clean_french(cat3.title)) 
    WHEN amadou.proper_case(amadou.clean_french(cat3.title)) = "Annonces" THEN amadou.proper_case(amadou.clean_french(cat2.title)) 
    WHEN amadou.proper_case(amadou.clean_french(cat2.title))= "Annonces" THEN amadou.proper_case(amadou.clean_french(horizon.categories.title)) 
    ELSE "" END AS category,
    CASE WHEN amadou.proper_case(amadou.clean_french(cat5.title)) = "Annonces" THEN amadou.proper_case(amadou.clean_french(cat3.title)) 
    WHEN amadou.proper_case(amadou.clean_french(cat4.title)) = "Annonces" THEN amadou.proper_case(amadou.clean_french(cat2.title)) 
    WHEN amadou.proper_case(amadou.clean_french(cat3.title)) = "Annonces" THEN amadou.proper_case(amadou.clean_french(horizon.categories.title)) 
    ELSE "" END AS subcategory,
    l.deleted_at,
    is_paid
FROM
    horizon.listings l 
INNER JOIN 
    horizon.listing_statuses ls ON l.listing_status_id = ls.id
INNER JOIN 
    horizon.categories ON l.category_id = categories.id
LEFT JOIN 
    horizon.categories cat2 ON categories.parent_id = cat2.id
left join 
    horizon.categories cat3 ON cat2.parent_id = cat3.id
left join
    horizon.categories cat4 on cat3.parent_id = cat4.id
left join
    horizon.categories cat5 on cat4.parent_id = cat5.id
INNER JOIN 
    horizon.listing_types lt ON l.listing_type_id = lt.id 
WHERE
    l.domain_id = 5

---------------------------


use horizon;
CREATE VIEW order_vue AS
select 
oi.id as order_item_id,
oi.product_id as product_id,
oi.order_status_id,
oi.price,
oi.credits,
oi.expires,
oi.created_at as created_at_order_item,
oi.updated_at as update_at_order_item,
o.id as order_id,
o.payment_method_id,
o.user_id,
o.user_device,
o.created_at as created_at_order,
o.updated_at as updated_at_order

FROM
    horizon.order_items AS oi 
INNER JOIN
    horizon.orders AS o ON o.id = oi.order_id 
WHERE
    o.domain_id = 5 
AND
    oi.entity_type = 'listing'


Calendrier = ADDCOLUMNS(
    CALENDAR( Date( 2021, 1,1) , Date( 2023,12, 31) ),
     "YEAR",YEAR([Date]),
     "Quarter" , FORMAT( [Date] , "Q"),
     "MONTH",FORMAT([Date], "MMMM"),
     "DAY", FORMAT( [Date] , "dddd"),
     "No Month" , MONTH( [Date]),
     "No Day" , WEEKDAY( [Date],2 )
   
   

)





---------------options de visibilté ---------------------

SET @start_date = '2023-01-01';
SELECT
    l.user_id as user_id,
    p.title AS  option_visibilitée,
    o.id as id_commande,
    l.id as id_listing,
    i.price,
    l.created_at as date_listing,
    o.created_at as date_orders,
    i.credits
FROM
    horizon.orders o
INNER JOIN
    horizon.order_items i ON i.order_id = o.id
INNER JOIN
    horizon.listings l ON l.id = i.entity_id
INNER JOIN
    horizon.categories c ON l.category_id = c.id
INNER JOIN
    horizon.products p ON p.id = i.product_id
-- inner join horizon.payments py ON py.order_id = o.id
WHERE
    o.domain_id = 5
    AND i.entity_type = 'listing'
    AND o.order_status_id = 3
    AND DATE(o.created_at) >= @start_date
    AND payment_method_id = 18
