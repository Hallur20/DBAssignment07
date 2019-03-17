<h1 align="center">DBAssignment07</h1>

<p>This is a solution for the following databases assignment: https://github.com/datsoftlyngby/soft2019spring-databases/blob/master/assignments/Assignment7.md</p>

<h1>Setup <g-emoji class="g-emoji" alias="checkered_flag" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f3c1.png">🏁</g-emoji></h1>

<p>In order to create the "CustomerOverview" materialized use the following query:</p>
  
```sql
  CREATE 
    ALGORITHM = UNDEFINED 
    DEFINER = `root`@`%` 
    SQL SECURITY DEFINER
VIEW `CustomerOverview` AS
    SELECT 
        `customers`.`customerNumber` AS `customerNumber`,
        `customers`.`customerName` AS `customerName`,
        CONCAT(`customers`.`contactFirstName`,
                ' ',
                `customers`.`contactLastName`) AS `contactName`,
        `customers`.`phone` AS `contactPhone`,
        `customers`.`city` AS `custCity`,
        `customers`.`postalCode` AS `custZip`,
        `customers`.`country` AS `custCountry`,
        CONCAT(`employees`.`firstName`,
                ' ',
                `employees`.`lastName`) AS `repName`,
        `employees`.`email` AS `repEmail`,
        `offices`.`phone` AS `repPhone`,
        `offices`.`postalCode` AS `repZip`,
        `offices`.`city` AS `repCity`,
        CONCAT(`offices`.`addressLine1`,
                '
                ',
                `offices`.`addressLine2`) AS `repAddress`,
        `offices`.`country` AS `repCountry`
    FROM
        ((`employees`
        JOIN `customers` ON ((`employees`.`employeeNumber` = `customers`.`salesRepEmployeeNumber`)))
        JOIN `offices` ON ((`employees`.`officeCode` = `offices`.`officeCode`)))
```
<h1>Exercises<g-emoji class="g-emoji" alias="page_with_curl" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f4c3.png">📃</g-emoji></h1>

<h2>Exercise 1</h2>


 <table>
<thead>
<tr>
<th align="left">Colums</th>
<th align="center">1_customerNumber</th>
<th align="center">2_customerName</th>
<th align="center">3_contactName</th>
<th align="center">4_contactPhone</th>
<th align="center">5_custCity</th>
<th align="center">6_custZip</th>
<th align="center">7_custCountry</th>
 <th align="center">8_repName</th>
 <th align="center">9_repEmail</th>
 <th align="center">10_repPhone</th>
 <th align="center">10_repZip</th>
 <th align="center">11_repCity</th>
 <th align="center">12_repAddress</th>
 <th align="center">13_repCountry</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><strong>Dependent</strong></td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">1</td>
<td align="center">8</td>
<td align="center">8</td>
<td align="center">10</td>
<td align="center">11</td>
<td align="center">8</td>
<td align="center">8</td>

</tr>
</tbody>
</table>

<p>We conclude that the view violates a secound normal form, because there is some columns in the view table that contains multiple values.</p>


e3:

we created a full text indext on the phone in offices in order to not get the "you are using safe update mode" error.
index helps with: function-based index that avoids the implicit type conversion.
->insert example on full text index thing<-
  
  then we were able to change the repPhone (we also change offices phone because else the repPhone would not make sense):
  
  update offices set phone = "+1 212 555 4000" where (phone = "+1 212 555 3000" AND officeCode <> 0);
update CustomerOverview set repPhone = "+1 212 555 4000" where (repPhone in ("+1 212 555 3000") AND customerNumber <> 0);
  
  next question in e3:
  
  update CustomerOverview set repEmail = "newEmail@classicmodelcars.com" where repName = "Leslie Jennings";
  our expectation to this was that only the view would be affected, but to our surprise the employees table was also updated.
  Then we started to research where thinsg could go wrong and we found out that some of the "rep's" were having the same phone numbers. (example: leslie jennings and leslie thompson), so they are in family which could cause issues if updating by the number. So a silly way of doing it would be this: set repEmail = "newEmail@classicmodelcars.com" where repPhone = "+1 650 219 4782";
  
  <img src="https://github.com/Hallur20/DBAssignment07/blob/master/tegning.png"/>
