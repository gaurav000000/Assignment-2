# Assignment-2
Functions
----
1) customer registration
DELIMITER //
CREATE FUNCTION customer cegistration (
D. customer name VARCHAR (50), P customer phone BIGINT, p customer address TEXT
I
RETURNS BIGINT DETERMINISTIC
BEGIN
DECLARE dummy.phone BIGINT;
DECLARE total customers INT;
DECLARE CONTINUE HANDLER FOR SQLSTATE '23000' SET Rcustomer phone = NULL;
SELECT COUNT*) INTO total customers FROM customer details;
SET dummy phone = R customer phone - total customers;
BEGIN
INSERT INTO customer details(customer_name, customer_phone, customer _address, date_of_registration)
VALUES (R_customer name.P customer phone. customer address,curdate());
END;
IF Rcustomer phone IS NULL THEN
INSERT INTO customer details (customer name, customer phone, customer address, date of registration)
VALUES (Rcustomer name, dummy.phone, P. customer address.CURRATE());
RETURN dumny phone;
ELSE
RETURN P. customer.phone:
END IF;
END;

2) get total earnings
DELIMITER //
CREATE FUNCTION get total earnings (R driver id INT)
RETURNS VARCHAR (100)
DETERMINISTIC
BEGIN
DECLARE total pool fare DECIMAL (10,2);
DECLARE total
solo face DECIMAL (10,2) ;
DECLARE total earnings VARCHAR (100);
IF NOT EXISTS (SELECT 1 FROM driver details WHERE driver id = driver id) THEN
RETURN "-1";
END IF;
SELECT SUM(p.fare - (R.face * 0.05)) INTO total Peol fare
FROM payment details p INNER JOIN booking details b ON b.booking id = p. booking id
WHERE b.driver id = p.driver id AND b. type of travel = 'pool';
IF total poot fare IS NULL THEN
SET total pool fare = 0;
END IF;
SELECT SUM(R.fare - (R.face * 0.10)) INTO total
solo fare
FROM payment details p INNER JOIN booking details b ON b. booking id = R. booking id
WHERE b.driver id = Rdriver id AND b. type of travel = 'solo';
IF total solo fare IS NULL THEN
SET total solo fare = 0;
END IF;
SET total earnings = CONCAT( 'Solo Trip:', total solo fare, '-Pool Trip:', total pool fare);
RETURN total earnings;
END
//
DELIMITER ;
3) update credit points
DELIMITER //
CREATE FUNCTION update credit points(
Rcustomer_ id INT,
P.fare DECIMAL (6,2)
)
RETURNS INT
DETERMINISTIC
BEGIN
DECLARE completed_ credit points DECIMAL (10,2);
DECLARE cancelled_credit_ points INT;
DECLARE current journey credit points DECIMAL (10,2);
DECLARE total credit points DECIMAL (10,2);
DECLARE updated_credit points INT;
IF NOT EXISTS (SELECT 1 FROM booking details WHERE customer_id = p.customer_id) THEN
RETURN -1;
END IF;
SELECT SUM(fare) INTO completed credit points
FROM payment details INNER JOIN booking_details ON booking details.booking_id = payment_details.booking id
WHERE customer_id = p_customer_ id AND booking_status ='completed';
SET completed credit points = (completed credit points * (SELECT COUNT(*) FROM booking details WHERE customer_id = R_custoner_ id AND booking status = 'completed*)) /
SELECT COUNT (*) INTO cancelled credit_points
FROM booking details
WHERE customer_id = P_customer_id AND booking_status = 'cancelled';
SET cancelled_credit points = cancelled_ credit_points * 5;
SET current journey credit points = p_fare / 5;
SET total credit points = current journey credit points + completed credit points - cancelled credit points;
SET updated_credit points = ROUND(total credit points);
UPDATE customer details
SET credit points = GREATEST (updated_credit points, 0)
WHERE customer_id = p._customer_id;
RETURN (SELECT credit points FROM customer details WHERE
customer_id = p_customer_id);
END;
//
DELIMITER :

4) get discount
delimiter //
create function get discount (
P.amount decimal(6, 2),
P. coupon code varchar (50)
returns decimal (6, 2)
DETERMINISTIC
begin
declare y discount decimal (6, 2);
select discount percentage into v discount from coupon details
where coupon code = p.coupon code and expiry date >= curdate() and curdate() ›= expiry date;
if y discount is not null then
return P._amount - (Pamount * y_discount / 100);
else
return P_amount;
end if;
end //


6) allocate driver
DELIMITER //
CREATE FUNCTION allocate driver(
p.source VARCHAR (50),
P. destination VARCHAR(50),
p..number.of
people INT
)
RETURNS INT
BEGIN
DECLARE selected driver_ id INT;
IF P_type_of_travel = 'pool' THEN
SELECT driver_id INTO selected driver id -
FROM driver_details
WHERE current_trip_type = 'pool'
AND current trip source = P_source
AND current trip destination = p destination
ORDER BY total_bookings ASC
LIMIT 13
SET @available_seats := seating_capacity - p number of people;
WHILE @available_seats < 0 DO
SELECT driver_ id INTO selected driver id
FROM driver details
WHERE current trip type = 'pool'
ORDER BY total bookings ASC
LIMIT 1;
SET @available_seats:= seating capacity - pnumber of people;
END WHILE;
ELSEIF p. type of travel = 'solo' THEN
SET @available_seats := seating capacity - P_number of people;
WHILE @available_seats ‹ 0 DO
SELECT driver id INTO selected driver id
FROM driver details
WHERE current.
trip type = 'pool'
ORDER BY total bookings ASC
LIMIT 1;
SET @available_seats := seating_capacity - Pnumber_of_people;
END WHILE;
ELSEIF R_type_of_travel = 'solo' THEN
SELECT driver id INTO selected driver id
FROM driver details
WHERE last_completed trip source = P._source
AND last_ completed
_trip status = 'completed'
AND current_trip_type IS NULL
ORDER BY total_bookings ASC, average_cating DESC
LIMIT 1;
IF selected_driver_id IS NULL THEN
SELECT driven.
id INTO selected driver id
FROM driver details
WHERE current trip type IS NULL
ORDER BY total_bookings ASC, average_ rating DESC
LIMIT 1;
END IF;
END IF;
RETURN selected driver id;
END;

