Q1)
SELECT d.name, u.usage_type, COUNT(*) AS total_installations
FROM installation i
JOIN user u ON i.house_no = u.house_no
JOIN distributor d ON i.tin_no = d.tin_no
WHERE u.usage_type = 'Domestic'
GROUP BY d.name, u.usage_type
ORDER BY total_installations DESC
LIMIT 1;

Q2)
SELECT u.area, MAX(s.capacity) AS highest_capacity
FROM installation i
JOIN user u ON i.house_no = u.house_no
JOIN solar_panel s ON i.pv_module = s.pv_module
GROUP BY u.area

Q3)
SELECT u.area
FROM installation i
JOIN user u ON i.house_no = u.house_no
JOIN solar_panel s ON i.pv_module = s.pv_module
WHERE s.pv_type = 'Monocrystalline'
GROUP BY u.area;

Q4)
SELECT s.pv_type, SUM(s.installation_charge) AS total_charge
FROM installation i
JOIN user u ON i.house_no = u.house_no
JOIN solar_panel s ON i.pv_module = s.pv_module
WHERE u.area = 'Bangalore'
GROUP BY s.pv_type;

Q5)
SELECT d.name, s.pv_module, s.installation_charge, s.pv_type, s.capacity, 
       MIN(i.installation_date) AS installation_date
FROM installation i
JOIN distributor d ON i.tin_no = d.tin_no
JOIN solar_panel s ON i.pv_module = s.pv_module
GROUP BY d.name, s.pv_module, s.installation_charge, s.pv_type, s.capacity
ORDER BY installation_date ASC
LIMIT 1; 

Q6)
SELECT s.pv_type, AVG(s.installation_charge) AS avg_sales
FROM installation i
JOIN user u ON i.house_no = u.house_no
JOIN solar_panel s ON i.pv_module = s.pv_module
WHERE u.usage_type = 'Commercial'
GROUP BY s.pv_type;

CREATE TABLE user (
    house_no INT PRIMARY KEY,
    area VARCHAR(30),
    usage_type VARCHAR(20)
);

CREATE TABLE solar_panel (
    pv_module INT PRIMARY KEY,
    pv_type VARCHAR(30),
    capacity INT,
    installation_charge INT
);

CREATE TABLE distributor (
    tin_no INT PRIMARY KEY,
    name VARCHAR(30)
);

CREATE TABLE soldby (
    tin_no INT,
    pv_module INT,
    PRIMARY KEY (tin_no, pv_module),
    FOREIGN KEY (tin_no) REFERENCES distributor(tin_no),
    FOREIGN KEY (pv_module) REFERENCES solar_panel(pv_module)
);

CREATE TABLE purchasedby (
    house_no INT,
    pv_module INT,
    PRIMARY KEY (house_no, pv_module),
    FOREIGN KEY (house_no) REFERENCES user(house_no),
    FOREIGN KEY (pv_module) REFERENCES solar_panel(pv_module)
);

CREATE TABLE installation (
    installation_id INT PRIMARY KEY,
    installation_date DATE,
    house_no INT,
    pv_module INT,
    tin_no INT,
    FOREIGN KEY (house_no) REFERENCES user(house_no),
    FOREIGN KEY (pv_module) REFERENCES solar_panel(pv_module),
    FOREIGN KEY (tin_no) REFERENCES distributor(tin_no)
);

INSERT INTO user VALUES
(1,'Bangalore','Domestic'),
(2,'Mysore','Commercial'),
(3,'Bangalore','Domestic'),
(4,'Hubli','Commercial');

INSERT INTO solar_panel VALUES
(101,'Monocrystalline',500,40000),
(102,'Polycrystalline',700,60000);

INSERT INTO distributor VALUES
(201,'SunPower'),
(202,'GreenSolar');

INSERT INTO soldby VALUES
(201,101),
(201,102),
(202,102);

INSERT INTO purchasedby VALUES
(1,101),
(1,102),
(2,102),
(3,101),
(4,102);

INSERT INTO installation VALUES
(1,'2018-01-10',1,101,201),
(2,'2016-03-15',1,102,201),
(3,'2019-05-20',2,102,202),
(4,'2020-07-25',3,101,201),
(5,'2021-09-30',4,102,202);
