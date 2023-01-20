-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema beauty_db
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema beauty_db
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `beauty_db` ;
USE `beauty_db` ;

-- -----------------------------------------------------
-- Table `beauty_db`.`Customer`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`Customer` (
  `customerID` INT NOT NULL AUTO_INCREMENT,
  `firstname` VARCHAR(45) NOT NULL,
  `lastname` VARCHAR(45) NOT NULL,
  `phone` VARCHAR(15) NULL,
  `email` VARCHAR(45) NULL,
  PRIMARY KEY (`customerID`),
  INDEX `Customer_firstname_lastname_idx` (`firstname` ASC, `lastname` ASC),
  CONSTRAINT `chk_phone_email_null`
	CHECK (`phone` is not null or `email` is not null))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `beauty_db`.`Employee`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`Employee` (
  `employeeID` INT NOT NULL AUTO_INCREMENT,
  `firstname` VARCHAR(45) NOT NULL,
  `lastname` VARCHAR(45) NOT NULL,
  `phone` VARCHAR(45) NOT NULL,
  `email` VARCHAR(45) NOT NULL,
  `street` VARCHAR(45) NOT NULL,
  `zipcode` VARCHAR(45) NOT NULL,
  `city` VARCHAR(45) NOT NULL,
  `payrate` DECIMAL(4,2) NOT NULL,
  PRIMARY KEY (`employeeID`),
  INDEX `Employee_firstname_lastname_idx` (`firstname` ASC, `lastname` ASC),
  INDEX `Employee_payrate_idx` (`payrate` ASC),
  CONSTRAINT `chk_payrate`
	CHECK (`payrate` >= 0))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `beauty_db`.`Service`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`Service` (
  `serviceID` INT NOT NULL AUTO_INCREMENT,
  `servicename` VARCHAR(128) NOT NULL,
  `serviceprice` DECIMAL(6,2) NOT NULL,
  `serviceduration` TIME NOT NULL,
  PRIMARY KEY (`serviceID`),
  INDEX `Service_servicename_idx` (`servicename` ASC),
  INDEX `Service_serviceprice_idx` (`serviceprice` ASC),
  INDEX `Service_serviceduration_idx` (`serviceduration` ASC),
  CONSTRAINT `chk_serviceprice` 
	CHECK (`serviceprice` >= 0))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `beauty_db`.`ProductGroup`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`ProductGroup` (
  `productgroupID` INT NOT NULL AUTO_INCREMENT,
  `groupname` VARCHAR(128) NOT NULL,
  `groupdescription` VARCHAR(1024) NOT NULL,
  PRIMARY KEY (`productgroupID`),
  INDEX `ProductGroup_groupname_idx` (`groupname` ASC))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `beauty_db`.`Product`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`Product` (
  `productID` INT NOT NULL AUTO_INCREMENT,
  `productname` VARCHAR(128) NOT NULL,
  `manufacturer` VARCHAR(128) NOT NULL,
  `stock` INT NOT NULL,
  `productdescription` VARCHAR(1024) NOT NULL,
  `category` INT NOT NULL,
  `productprice` DECIMAL(6,2) NOT NULL,
  `sellingprice` DECIMAL(6,2) GENERATED ALWAYS AS (`productprice` * 1.24 * 3.33),
  PRIMARY KEY (`productID`),
  INDEX `fk_Product_ProductGroup1_idx` (`category` ASC),
  INDEX `Product_stock_idx` (`stock` ASC),
  INDEX `Product_productprice_idx` (`productprice` ASC),
  INDEX `Product_sellingprice_idx` (`sellingprice` ASC),
  CONSTRAINT `fk_Product_Productgroup1`
    FOREIGN KEY (`category`)
    REFERENCES `beauty_db`.`ProductGroup` (`productgroupID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `chk_stock` 
	CHECK (`stock` >= 0),
  CONSTRAINT `chk_productprice`
	CHECK (`productprice` >= 0))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `beauty_db`.`Appointment`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`Appointment` (
  `appointmentID` INT NOT NULL AUTO_INCREMENT,
  `Customer_customerID` INT NOT NULL,
  `Employee_employeeID` INT NOT NULL,
  `servicetype` INT NOT NULL,
  `booktime` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `starttime` DATETIME NOT NULL,
  PRIMARY KEY (`appointmentID`),
  INDEX `fk_Customer_has_Employee_Employees1_idx` (`Employee_employeeID` ASC),
  INDEX `fk_Customer_has_Employee_Customer_idx` (`Customer_customerID` ASC),
  INDEX `fk_Appointment_Service1_idx` (`servicetype` ASC),
  INDEX `Appointment_booktime_idx` (`booktime` ASC),
  INDEX `Appointment_starttime_idx` (`starttime` ASC),
  CONSTRAINT `fk_Customers_has_Employees_Customers`
    FOREIGN KEY (`Customer_customerID`)
    REFERENCES `beauty_db`.`Customer` (`customerID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_Customers_has_Employees_Employees1`
    FOREIGN KEY (`Employee_employeeID`)
    REFERENCES `beauty_db`.`Employee` (`employeeID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_Appointment_Service1`
    FOREIGN KEY (`servicetype`)
    REFERENCES `beauty_db`.`Service` (`serviceID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `beauty_db`.`Purchase`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `beauty_db`.`Purchase` (
  `purchaseID` INT NOT NULL AUTO_INCREMENT,
  `Customer_customerID` INT NOT NULL,
  `Product_productID` INT NOT NULL,
  `purchasedate` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `quantity` INT NOT NULL,
  PRIMARY KEY (`purchaseID`),
  INDEX `fk_Customer_has_Product_Products1_idx` (`Product_productID` ASC),
  INDEX `fk_Customer_has_Product_Customer1_idx` (`Customer_customerID` ASC),
  INDEX `Purchase_purchasedate_idx` (`purchasedate` ASC),
  INDEX `Purchase_quantity_idx` (`quantity` ASC),
  CONSTRAINT `fk_Customers_has_Products_Customers1`
    FOREIGN KEY (`Customer_customerID`)
    REFERENCES `beauty_db`.`Customer` (`customerID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_Customers_has_Products_Products1`
    FOREIGN KEY (`Product_productID`)
    REFERENCES `beauty_db`.`Product` (`productID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT chk_quantity
	CHECK (quantity >= 0))
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
