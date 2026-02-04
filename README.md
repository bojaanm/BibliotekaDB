Panevropski univerzitet “Apeiron”
Fakultet informacionih tehnologija
Akademska godina 2025/26.

Seminarski rad
Predmet: Baze podataka
Naziv teme rada: Biblioteka

Student: Milanović Bojan
Broj indeksa: 
E-mail: bojan.milanovic@apeiron-edu.eu
	Mentor:
Dipl. Inž. Radulović Milica
Banja Luka 2026.
Sadržaj
1. Uvod	2
1. Kreiranje tabela	3
2. Modeliranje relacione baze podataka	4
3. Tipovi podataka	5
4. Integritet podataka	6
4.1. Entitetski integritet	6
4.2. Referencijalni integritet	6
4.3. Domenski integritet	6
5. Unos podataka i ilustracija pristupa podacima	6
6. SQL naredbe (DDL i DML)	6
7. Funkcije u SQL-u	7
7.1. UDF funkcije	7
8. Pogledi (VIEW)	8
9. Uskladištene procedure	9
10. Trigeri	10
11. Indeksi	10
12. Upiti nad više tabela (JOIN)	11
13. Zaključak	12

1. Uvod
U okviru ovog seminarskog rada realizovana je relaciona baza podataka na temu biblioteka, koja predstavlja informacioni sistem za evidenciju knjiga, članova i procesa iznajmljivanja. Savremene biblioteke zahtijevaju pouzdane i efikasne baze podataka kako bi se obezbijedilo tačno praćenje dostupnosti knjižnog fonda, istorije zaduženja i rezervacija.
Cilj rada je da se kroz praktičan primjer prikaže kompletan proces dizajna i implementacije relacione baze podataka, počevši od modeliranja podataka, preko definisanja integriteta, pa sve do primjene naprednih SQL objekata. Poseban akcenat stavljen je na demonstraciju SQL upita i mehanizama koji obezbjeđuju konzistentnost i pouzdanost podataka.
Razvijena baza podataka omogućava osnovne funkcionalnosti sistema biblioteke, kao što su unos i pregled knjiga, iznajmljivanje i vraćanje knjiga, obračun kazni, kao i praćenje rezervacija. Rad je realizovan u MS SQL Server 2022 okruženju, sa nivoom kompatiblinosti MS SQL Server 2014.
________________________________________
1. Kreiranje tabela
U nastavku je prikazan dio SQL skripte za kreiranje tabela:
CREATE TABLE Kategorija (
    KategorijaID INT IDENTITY PRIMARY KEY,
    Naziv NVARCHAR(100) NOT NULL
);

CREATE TABLE Autor (
AutorID INT IDENTITY PRIMARY KEY,
    Ime NVARCHAR(50),
    Prezime NVARCHAR(50)
);
CREATE TABLE Knjiga (
    KnjigaID INT IDENTITY PRIMARY KEY,
    Naslov NVARCHAR(200) NOT NULL,
    GodinaIzdanja INT,
    KategorijaID INT,
    UkupnoPrimjeraka INT,
    DostupnoPrimjeraka INT,
    FOREIGN KEY (KategorijaID) REFERENCES Kategorija(KategorijaID)
);

CREATE TABLE KnjigaAutor (
    KnjigaID INT,
    AutorID INT,
    PRIMARY KEY (KnjigaID, AutorID),
    FOREIGN KEY (KnjigaID) REFERENCES Knjiga(KnjigaID),
    FOREIGN KEY (AutorID) REFERENCES Autor(AutorID)
);

CREATE TABLE Clan (
    ClanID INT IDENTITY PRIMARY KEY,
    Ime NVARCHAR(50),
    Prezime NVARCHAR(50),
    BrojClanskeKarte NVARCHAR(20),
    Telefon NVARCHAR(20),
    Email NVARCHAR(100)
);

CREATE TABLE Iznajmljivanje (
    IznajmljivanjeID INT IDENTITY PRIMARY KEY,
    ClanID INT,
    KnjigaID INT,
    DatumIznajmljivanja DATE,
    RokVracanja DATE,
    DatumVracanja DATE NULL,
    Kazna DECIMAL(6,2),
    FOREIGN KEY (ClanID) REFERENCES Clan(ClanID),
    FOREIGN KEY (KnjigaID) REFERENCES Knjiga(KnjigaID)
);

CREATE TABLE Rezervacija (
    RezervacijaID INT IDENTITY PRIMARY KEY,
    ClanID INT,
    KnjigaID INT,
    DatumRezervacije DATE,
    Status NVARCHAR(20),
    FOREIGN KEY (ClanID) REFERENCES Clan(ClanID),
    FOREIGN KEY (KnjigaID) REFERENCES Knjiga(KnjigaID)
);
________________________________________
2. Modeliranje relacione baze podataka
Modeliranje relacione baze podataka predstavlja ključni korak u razvoju informacionog sistema. U ovom radu izvršeno je logičko modeliranje podataka na osnovu realnih procesa koji se odvijaju u biblioteci.
Relaciona baza podataka sastoji se od sljedećih entiteta:
•	Clan – predstavlja korisnike biblioteke
•	Knjiga – sadrži informacije o knjižnom fondu
•	Autor – evidentira autore knjiga
•	Kategorija – omogućava klasifikaciju knjiga
•	Iznajmljivanje – evidentira proces posuđivanja knjiga
•	Rezervacija – prati rezervacije knjiga
•	KnjigaAutor – realizuje vezu između knjiga i autora
Na dijagramu su jasno označeni primarni ključevi, strani ključevi i veze između tabela, što omogućava lakše razumijevanje strukture baze.
 ________________________________________
3. Tipovi podataka
U tabelama su korišteni različiti tipovi podataka:
•	Numerički: INT, DECIMAL
•	Alfanumerički: NVARCHAR
•	Datumski: DATE
Datumski tipovi podataka korišteni su u više entiteta (Iznajmljivanje, Rezervacija), dok su decimalni tipovi upotrijebljeni za evidenciju kazni.
________________________________________
4. Integritet podataka
4.1. Entitetski integritet
Entitetski integritet obezbijeđen je definisanjem primarnih ključeva za svaku tabelu.
4.2. Referencijalni integritet
Referencijalni integritet je uspostavljen pomoću stranih ključeva, čime se obezbjeđuje konzistentnost podataka između povezanih tabela.
4.3. Domenski integritet
Domenski integritet ostvaren je korištenjem NOT NULL i CHECK ograničenja, čime se kontroliše validnost unesenih vrijednosti.
Primjer:
ALTER TABLE Knjiga
ADD CONSTRAINT CHK_Primjerci
CHECK (DostupnoPrimjeraka >= 0);
________________________________________
5. Unos podataka i ilustracija pristupa podacima
Radi testiranja funkcionalnosti baze, izvršen je unos početnih podataka:
INSERT INTO Clan VALUES
('Marko', 'Marković', '1001', '061111111', 'marko@mail.com');

INSERT INTO Kategorija VALUES ('Programiranje');

INSERT INTO Knjiga VALUES
('Uvod u SQL', 2022, 1, 5, 5);
________________________________________
6. SQL naredbe (DDL i DML)
U radu su korištene:
•	DDL naredbe: CREATE, ALTER
•	DML naredbe: INSERT, UPDATE, SELECT
Na ovaj način je demonstrirano upravljanje strukturom baze i rad sa podacima.
________________________________________
7. Funkcije u SQL-u
U radu su korištene:
•	Datumske funkcije (GETDATE, DATEADD, DATEDIFF)
DATEDIFF(day, i.DatumIznajmljivanja, GETDATE()) AS DaniIznajmljivanja
•	Funkcije za rad sa stringovima (CONCAT)
CONCAT(c.Ime, ' ', c.Prezime) AS PunoIme
•	Agregatne funkcije (COUNT)
COUNT(i.IznajmljivanjeID) AS BrojIznajmljivanja
7.1. UDF funkcije
Kreirane su dvije korisnički definisane funkcije:
•	Funkcija za izračunavanje kazne:
CREATE FUNCTION dbo.fn_Kazna
(
    @IznajmljivanjeID INT
)
RETURNS DECIMAL(6,2)
AS
BEGIN
    DECLARE @DaniKasnjenja INT;
    DECLARE @Kazna DECIMAL(6,2);

    SELECT @DaniKasnjenja = DATEDIFF(day, RokVracanja, ISNULL(DatumVracanja, GETDATE()))
    FROM Iznajmljivanje
    WHERE IznajmljivanjeID = @IznajmljivanjeID;

    IF @DaniKasnjenja > 0
        SET @Kazna = @DaniKasnjenja * 0.5;
    ELSE
        SET @Kazna = 0;

    RETURN @Kazna;
END
•	Funkcija za prikaz punog imena autora:
CREATE FUNCTION dbo.fn_PunoImeAutora
(
    @AutorID INT
)
RETURNS NVARCHAR(101)
AS
BEGIN
    DECLARE @ImePrezime NVARCHAR(101);

    SELECT @ImePrezime = CONCAT(Ime, ' ', Prezime)
    FROM Autor
    WHERE AutorID = @AutorID;

    RETURN @ImePrezime;
END;
________________________________________
8. Pogledi (VIEW)
Kreirana su tri pogleda:
•	vw_AktivnaIznajmljivanja
CREATE VIEW vw_AktivnaIznajmljivanja AS
SELECT *
FROM Iznajmljivanje
WHERE DatumVracanja IS NULL
 
•	vw_DostupneKnjige
CREATE VIEW vw_DostupneKnjige AS
SELECT Naslov
FROM Knjiga
WHERE DostupnoPrimjeraka > 0
 
•	vw_KnjigeAutori koji koristi UDF funkciju:
CREATE VIEW vw_KnjigeAutori AS
SELECT k.Naslov, dbo.fn_PunoImeAutora(ka.AutorID) AS Autor
FROM KnjigaAutor ka
JOIN Knjiga k ON ka.KnjigaID = k.KnjigaID;
 
________________________________________
9. Uskladištene procedure
Implementirane su tri procedure:
•	sp_IznajmiKnjigu
CREATE PROCEDURE sp_IznajmiKnjigu
@ClanID INT, @KnjigaID INT
AS
BEGIN
 INSERT INTO Iznajmljivanje
 VALUES (@ClanID, @KnjigaID, GETDATE(), DATEADD(day,14,GETDATE()), NULL, 0)
END
•	sp_VratiKnjigu
CREATE PROCEDURE sp_VratiKnjigu
@IznajmljivanjeID INT
AS
BEGIN
 UPDATE Iznajmljivanje
 SET DatumVracanja = GETDATE()
 WHERE IznajmljivanjeID = @IznajmljivanjeID;

DECLARE @Kazna DECIMAL(6,2);
SET @Kazna = dbo.fn_Kazna(@IznajmljivanjeID);

UPDATE Iznajmljivanje
SET Kazna = @Kazna
WHERE IznajmljivanjeID = @IznajmljivanjeID;

PRINT 'Kazna za ovo iznajmljivanje je: ' + CAST(@Kazna AS NVARCHAR(10));
END
•	sp_RezervisiKnjigu
CREATE PROCEDURE sp_RezervisiKnjigu
@ClanID INT,
@KnjigaID INT
AS
BEGIN
 INSERT INTO Rezervacija (ClanID, KnjigaID, DatumRezervacije,         Status)
 VALUES (@ClanID, @KnjigaID, GETDATE(), 'Aktivna');
END
Procedure omogućavaju automatizovano iznajmljivanje i vraćanje knjiga.
________________________________________
10. Trigeri
Kreirana su dva trigera:
•	Triger za smanjenje broja dostupnih primjeraka
CREATE TRIGGER trg_SmanjiPrimjerke
ON Iznajmljivanje
AFTER INSERT
 AS
  UPDATE Knjiga
  SET DostupnoPrimjeraka = DostupnoPrimjeraka - 1
 WHERE KnjigaID IN (SELECT KnjigaID FROM inserted)
•	Triger za povećanje broja dostupnih primjeraka prilikom vraćanja knjige
CREATE TRIGGER trg_PovecajPrimjerke
ON Iznajmljivanje
AFTER UPDATE
 AS
  IF UPDATE(DatumVracanja)
   UPDATE Knjiga
 SET DostupnoPrimjeraka = DostupnoPrimjeraka + 1
WHERE KnjigaID IN (SELECT KnjigaID FROM inserted);
Na ovaj način je obezbijeđena automatska kontrola stanja knjiga.
________________________________________
11. Indeksi
Radi optimizacije upita, kreirani su indeksi nad često korištenim kolonama:
CREATE INDEX idx_Naslov ON Knjiga(Naslov);
CREATE INDEX idx_Clan ON Iznajmljivanje(ClanID);

________________________________________
12. Upiti nad više tabela (JOIN)
Prikazane su sve varijante JOIN operacija:
•	INNER JOIN
SELECT 
    CONCAT(c.Ime, ' ', c.Prezime) AS PunoIme,
    k.Naslov,
    DATEDIFF(day, i.DatumIznajmljivanja, GETDATE()) AS DaniIznajmljivanja
FROM Iznajmljivanje i
INNER JOIN Clan c ON i.ClanID = c.ClanID
INNER JOIN Knjiga k ON i.KnjigaID = k.KnjigaID
WHERE i.DatumVracanja IS NULL
 


•	LEFT JOIN
SELECT 
    k.Naslov,
    COUNT(i.IznajmljivanjeID) AS BrojIznajmljivanja
FROM Knjiga k
LEFT JOIN Iznajmljivanje i ON k.KnjigaID = i.KnjigaID
GROUP BY k.Naslov
 
•	RIGHT JOIN
SELECT 
    r.DatumRezervacije,
    k.Naslov,
    CASE 
        WHEN k.DostupnoPrimjeraka < 2 THEN 'Malo primjeraka'
        ELSE 'Dovoljno primjeraka'
    END AS StatusPrimjeraka
FROM Rezervacija r
RIGHT JOIN Knjiga k ON r.KnjigaID = k.KnjigaID;
 
•	FULL OUTER JOIN
SELECT 
    CONCAT(c.Ime, ' ', c.Prezime) AS PunoIme,
    k.Naslov,
    COALESCE(CONVERT(NVARCHAR(20), i.DatumIznajmljivanja, 120), 'Nema iznajmljivanja') AS DatumIznajmljivanja
FROM Clan c
FULL OUTER JOIN Iznajmljivanje i ON c.ClanID = i.ClanID
FULL OUTER JOIN Knjiga k ON i.KnjigaID = k.KnjigaID;
 
Upiti demonstriraju povezivanje više tabela i dobijanje složenih rezultata.
________________________________________
13. Zaključak
U ovom seminarskom radu uspješno je realizovana kompletna relaciona baza podataka za biblioteku. Baza podataka je modelirana na realnom primjeru i obuhvata sve ključne entitete i relacije potrebne za funkcionisanje bibliotečkog sistema. Prikazana je upotreba različitih tipova podataka, kao i implementacija entitetskog, referencijalnog i domenskog integriteta, čime je obezbijeđena pouzdanost i konzistentnost podataka. Posebna pažnja posvećena je demonstraciji SQL naredbi, funkcija i naprednih objekata baze podataka. Korištenjem pogleda, uskladištenih procedura, trigerâ i korisnički definisanih funkcija omogućena je automatizacija procesa i olakšan rad sa podacima. Kreirani indeksi doprinose efikasnijem izvršavanju upita nad većim skupovima podataka. Ovakav sistem predstavlja dobru osnovu za dalje proširenje, kao što su dodavanje korisničkog interfejsa, evidencija zaposlenih, detaljnije upravljanje kaznama ili integracija sa web aplikacijama. Rad jasno demonstrira praktičnu primjenu teorijskog znanja iz oblasti baza podataka.
