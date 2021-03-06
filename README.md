# Database RisiKo

## Index

1. [Description and analysis of the problem](#Description-and-analysis-of-the-problem)
  * [Summary of the type of problem commissioned](#Summary-of-the-type-of-problem-commissioned)
  * [Implementation adopted](#Implementation-adopted)
    * [Constructive choices used to solve the problem](#Constructive-choices-used-to-solve-the-problem)
2. [Conceptual design](#Conceptual-design)
  * [Class diagram](#Class-diagram)
    * [Conventions adopted in the class diagram](#Conventions-adopted-in-the-class-diagram)
  * [Renewal of the class diagram](#Renewal-of-the-class-diagram)
    * [Conventions adopted in the revised class diagram](#Conventions-adopted-in-the-revised-class-diagram)
3. [Data dictionary](#Data-dictionary)
  * [Dictionary composition](#Dictionary-composition)
4. [Class dictionary](#Class-dictionary)
5. [Association dictionaries](#Association-dictionaries)
6. [Logical design](#Logical-design)
  * [Logical scheme](#Logical-scheme)
7. [Physical design](#Physical-design)
  * [Implementation details](#Implementation-details)
  * [Tables definition](#Tables-definition)
    * [Table definition: PARTITA](#Table-definition-PARTITA)
    * [Table definition: GIOCATORE](#Table-definition-GIOCATORE)
    * [Table definition: TURNO](#Table-definition-TURNO)
    * [Table definition: CARTA_OBIETTIVO](#Table-definition-CARTA_OBIETTIVO)
    * [Table definition: TERRITORIO](#Table-definition-TERRITORIO)
    * [Table definition: POSIZIONAMENTO_ARMATA](#Table-definition-POSIZIONAMENTO_ARMATA)
    * [Table definition: COMBATTIMENTO](#Table-definition-COMBATTIMENTO)
    * [Table definition: LANCIO_DADI](#Table-definition-LANCIO_DADI)
    * [Table definition: SPOSTAMENTO](#Table-definition-SPOSTAMENTO)
    * [Table definition: CONFINE](#Table-definition-CONFINE)
    * [Table definition: CARTA_TERRITORIO](#Table-definition-CARTA_TERRITORIO)
    * [Table definition: TERRITORIO_OCCUPATO](#Table-definition-TERRITORIO_OCCUPATO)
  * [Associations implement by tables](#Associations-implement-by-tables)
    * [Association table definition: Ass_Carta_Terr_Giocatore](#Association-table-definition-Ass_Carta_Terr_Giocatore)


# Description and analysis of the problem

## Summary of the type of problem commissioned

You write a database for storing games played according to the rules of the well-known game "Risiko!".
The database must contain all the information necessary to trace the game step by step
in all its details. In addition, all appropriate constraints are checked.

## Implementation adopted
### Constructive choices used to solve the problem

Mainly the database has been structured about each game and automation
of certain actions. Its construction is developed taking into account the regulation
classical game by excluding the "tournament rules".

The logical process saves the information about every single game in the database
following a straight line. First, defines what are the elements of a 
persistent project; such as objective cards, territories, and the entire communication system boundaries
among the territories for creating the game map. These elements represent the base on which
to carry out the whole project.

To break a single game on the other, as well as a player on the other, they are introduced
numeric keys that allow the identification of a game or a player in the manner
more consonant. The entire database works with these numeric keys for the later step execution
step of a game. This brings each game a numeric value, as well as the
single players to which a numeric value from 1-6 will be associated. The union of the two numeric values
lets you locate a given player in a chosen game. This management represents the part
not visible to the user. The end user will be able to use the "names" of the players and the matches set in
Priority will then be the database to formulate the search using the numeric keys.

Defined the game and the players, as by regulation there are three stages on which it develops
game: Armed Forces, Fighting and Moving. All three phases are managed through shifts. A turn represents the exact occurrence of all three phases. It is managed by
increasing sequential numeric values. Each player's turn will be associated with the player
three phases. The choice of the player to handle the turn is handled by the database.
These associates with each player, in addition to his previously seen ID, another numeric value
is randomly chosen from the set of values ranging from one to the exact number of players
(for example, a 4 player game will make the set of values 1 to 4). These values associated with the players determine, from the smallest to the greatest, the exact order they come from
played the round of a match.

The management of all present numeric values is done by avoiding the use of structures such as sequences. These should be generated and set in the correct way for each game creation.
To avoid such problems, the database has been structured to handle it simply and clean all the numeric values (a trivial example is if you want to increase the numeric value of the
turn to go to the next turn. The database will simply take the numeric value of the turn
previous and will increase it by one unit, obtaining a new turn).

The only structure created by the database, using dynamic SQL, for each game is a view containing
the number of territories won, for each continent, of each player.
The use of the view allows you to know if a player has all the territories of one or more
continents, as well as no more than any territory. The view plays a fundamental role
especially in the last case. As said every turn is associated with a player. Before starting the three
gambling steps are checked through the view if the player holds at least one territory. If not
there are territories then you will simply go to the next player.

Returning to the three stages of play, their execution order is bound by the application. It will be this
which will prevent the user, for example, from executing the second combat phase and then the first
armed arrangement phase. Most of the checks that have been made have been
implemented both on the side of the database and on the application side. Initially, the choice was weighted in dividing the i
controls between database and application. It was preferred not to proceed in this direction as it is
wanted to create a database capable of self-management even without the intervention of a written application
ad hoc to make various entries. This allows the database to handle step by step progress
of a game because it has all the controls created based on the game rules. Furthermore
allows you to make the database easier to handle if you decide to rewrite the application, in
part or all.

A final analysis is to be made for those that were initially defined as "automation of certain
actions. "As far as the database has the sole purpose of storing data, it has been
introduced techniques that allow the taking of certain actions more simply and cleanly.
An example is the one seen before, ie the task of asking the database for automatic selection
of the lap they will have to make players. The same is done when you proceed to
start a game. The phase involves the mixing of territorial cards and then entrusting them, an ad
one, each player. The same for target assignment (a player's goal). This
phase is completely managed from the database allowing the end user to have it already available
all the information he needs.

# Conceptual design

## Class diagram

Database design begins, starting with the highest abstraction level.
From the requirements analysis will develop a conceptual framework independent of the data structure and
from physical implementation. This will be developed using a Class Diagram UML that will have it
The aim is to represent, at a theoretical level, the entities and relationships that will then be developed and implemented
in the database.

### Conventions adopted in the class diagram

It was preferred to explicitly indicate every multiplicity; ignoring the convention that it does not express those of multiplicity [1]. Furthermore
every description of the relationships and entities was preferred to integrate directly into the class diagram.
This is to make the scheme more readily available to the reader. Without having to resort to
appendices aside, but directly answering the notes that are fully supported
from the UML standard.

![1main](https://cloud.githubusercontent.com/assets/22590804/23465503/bc895f94-fe98-11e6-98df-52f8e5253247.png)


## Renewal of the class diagram

To move the class diagram of the previous pages to a lower abstraction level proceed
with the restructuring of the same.
All specializations, structured and multiple attributes, and finally, hierarchies will be eliminated.

### Conventions adopted in the revised class diagram

The same road was followed
for the previous logic scheme. Then the multiplicities of [1] will be explicitly made
and notes and any integration has been directly inserted in the scheme in full respect
of the UML standard.

![main](https://cloud.githubusercontent.com/assets/22590804/23475044/5c7124a2-feb6-11e6-9b2e-29d1ad783c80.png)


# Data dictionary
## Dictionary composition

The data dictionary consists of two parts:
- Class Dictionary.
- Association dictionaries.

In the first you get information about the classes implemented in the database.
Here is a brief description of the following class of attribute attributes. For
each attribute specifies the type, its implementation within the database, and a description
that shows its use.
In the second part, the associations are analyzed according to the class renovation model
diagram.
Each association is highlighted by a name, followed by a brief description showing the correlation
between the two classes.
Finally, the cardinalities and the meaning of the association between the two classes are analyzed.

# Class Dictionary


| CLASS           | DESCRIPTION                  | ATTRIBUTES                                                                                                            |
| ---------------- |:----------------------------:| -------------------------------------------------------------------------------------------------------------------- |
| **Partita**          | Matches in progress and terminated | **ID_partita** [numeric, precision 6]: uniquely identifies each single game with an increasing numeric value |
|   |   | **Nome_partita** [string, length 30]: name assigned to the game |
|   |   | **Data** [date]: date of creation of the game. Use sysdate to get the current format date “DD-Mon-YYYY” |
|   |   | **ID_gamer_vinc** [numeric, precision 1]: The winning player ID of the match in question. If a winner is not yet present, his value is null |
| | | |
| **Giocatore**        | Participating players       | **ID_gamer** [numeric, precision 1]: numeric value (1 to 6) associated with each player in each game |
|   |   | **ID_partita** [numeric, precision 6]: expressed in the Partita class |
|   |   | **Colore** [character, length 1]: color chosen by the player's armies |
|   |   | **Nickname** [string, length 30]: name chosen by the player and used to identify in a game |
|   |   | **ID_carta_obiettivo** [numeric, precision 6]: Numeric value associated with a target card |
|   |   | **Posizione_turno** [numeric, precision 1]: sets the sequence in which players will perform their actions during the match |
|   |   | **N_armate_tot** [numeric, precision 32, default 0]: total armies that the player has on the entire map of the game |
| | | |
| **Carta_Territorio** | Maps related to territories      |  **Data** [date]: date of creation of the game. Use sysdate to get the current format date “DD-Mon-YYYY” |
|   |   | **Territorio** [string, length 400]: name of the reference territory |
|   |   | **Simbolo_Carta** [character, length 1]: a symbol associated with the paper. It can be: J (Jolly), O (Objective), F (Infantry), C (Cavalry), A (Artillery) |
| | | |
| **Carta_Obiettivo** | Objective cards    | **ID_Carta** [numeric, precision 6]: expresses the value associated with a card |
|   |   | **Descrizione** [string, length 400]: description of the lens |
| | | |
| **Turno** | Playing round   | **ID_turno** [numeric, precision 6, default pari a 0]: increasing numeric value that identifies the turn in which a player performs gaming actions|
|   |   | **ID_partita** [numeric, precision 6]: expressed in class Partita |
|   |   | **ID_gamer** [numeric, precision 1]: expressed in class Giocatore |
| | | |
| **Posizionamento_Armata** | First stage of play: positioning the armies   | **ID_Posizionamento** [numeric, precision 6]: uniquely identifies placements made by the player in the current turn |
|   |   | **ID_partita** [numeric, precision 6]: expressed in class Partita |
|   |   | **ID_Turno** [numeric, precizione 6]: expressed in class Turno |
|   |   | **ID_Territorio_Pos** [numeric, precision 2]: expressed in class Territorio |
|   |   | **Rinforzi_Truppe** [numeric, precision 3]: number of troops inserted |
|   |   | **Tipo_Posizionamento** [numeric, precision 1]: Specifies the type of positioning performed. It can be: 0 (Prefetching), 1 (Classic Entry), 2 to 8 (Entries derived from the combination of cards selected) |
| | | |
| **Combattimento** | Second stage of play: fighting    | **ID_Partita** [numeric, precision 6]: expressed in class Partita |
|   |   | **ID_Turno** [numeric, precision 6, default pari a 0]: expressed in class Turno |
|   |   | **ID_Comb** [numeric, precision 6]: uniquely identifies the fights performed by the player in the current round |
|   |   | **ID_Territorio_Attaccante** [numeric, precision 2]: identifying the territory that attacks |
|   |   | **ID_Territorio_Attaccato** [numeric, precision 2]: identifying the territory that it defends |
|   |   | **ID_G_Difensore** [numeric, precision 1]: player who owns the attacked territory |
|   |   | **Vincente** [numeric, precision 1]: place equal to 1 if the player has conquered the territory, 0 else |
|   |   | **Tipo_Avanzamento** [numeric, precision 1]: equal to 1 if you move N armed as the number of nuts launched, 2 if all armies are moved except one |
| | | |
| **Spostamento** | Third stage of play: armed movement | **ID_Turno** [numeric, precision 6, default pari a 0]: expressed in class Turno |
|   |   | **ID_Partita** [numeric, precision 6]: expressed in class Partita |
|   |   | **ID_Territorio_Partenza** [numeric, precision 2]: identifying the territory from which to take the armed forces |
|   |   | **ID_Territorio_Arrivo** [numeric, precision 2]: identifying the territory where armies are added |
|   |   | **Truppe_Spostate** [numeric, precision 9]: number of troops moved |
| | | |
| **Lancio_Dadi** | Launch Combat Dice | **ID_Partita** [numeric, precision 6]: expressed in class Partita |
|   |   | **ID_Turno** [numeric, precision 6, default pari a 0]: expressed in class Turno |
|   |   | **ID_Comb** [numeric, precision 6]: expressed in class Combattimento |
|   |   | **N_Lancio** [numeric, precision 6]: uniquely identifies the dice rolls made by the player in a turn |
|   |   | **DadoN_Att** [numeric, precision 1]: the value associated with the nth-nut of the attacking player |
|   |   | **DadoN_Diff** [numeric, precision 1]: value associated with the N-th Defender Player's Nut |
| | | The nuts values are between 1 and 6. The nuts that are not launched are null |
| | | |
| **Territorio_Occupato** | Information about the territories owned by the players | **ID_Partita** [numeric, precision 6]: expressed in class Partita |
|   |   | **ID_Giocatore_Occupante** [numeric, precision 1]: player identifier to which the territory belongs |
|   |   | **ID_Territorio** [numeric, precision 2]: expressed in class Territorio |
|   |   | **Quantita_truppe** [numeric, precision 9, default 1]: number of troops occupying the territory |
| | | |
| **Confine** | Confini territori | **ID_Territorio** [numeric, precision 2]: expressed in class Territorio |
|   |   | **ID_Confinante** [numeric, precision 2]: value(s) of adjacent territories |
| | | |
| **Territorio** | Territori di gioco | **ID_Territorio** [numeric, precision 2]: expressed in class Territorio |
|   |   | **Nome_Territorio** [string, length 60]: name associated with a territory ID |
|   |   | **ID_Continente** [numeric, precision 1]: identifies the continent of belonging to a territory |
|   |   | **Nome_Continente** [string, length 60]: name associated with a continent's ID |
| | | |
| **ASS_Carta_Territorio_Giocatore** | Indicates possession of Territory cards | **ID_Carta** [numeric, precision 6]: expresses the value associated with a card |
|   |   | **ID_Gamer** [numeric, precision 1]: expressed in class Giocatore |
|   |   | **ID_Partita** [numeric, precision 6]: expressed in class Partita |
| | | |

# Association dictionaries

| NAME             | DESCRIPTION                  | CLASSES INVOLVED                                                                                                     |
| ---------------- |:----------------------------:| -------------------------------------------------------------------------------------------------------------------- |
| Initial phase          | It gives you the ability to create a game by specifying players | **Partita → Giocatore** [3-6]: indicates the restriction of the minimum and maximum players present in a game |
|   |   | **Giocatore → Partita** [1]: expresses the unique membership of a player to a game |
| | | |
| Progress        | It expresses the union of players in the game shifts | **Giocatore → Turno** [1..*]: each player holds at least one turn of play |
|   |   | **Turno → Giocatore** [1]: every turn belongs to a single player |
| | | |
| Initialization | It expresses a turn of the stage |  **Turno → Preliminare** [0..1]: a turn can be either a preliminary or a "classic" round of play |
|   |   | **Preliminare → Turno** [1]: every preliminary need for a turn |
| | | |
| First phase | It expresses the first phase of play, the positioning of the armed forces | **Turno → Posizionamento_armate** [1..*]: N armament placements can be made |
|   |   | **Posizionamento_armate → Turno** [1]: ogni posizionamento appartiene ad un unico turno |
| | | |
| Second phase | It expresses the second stage of play, combat | **Turno → Combattimento** [0..*]: combat can be optional or N combat can be used |
|   |   | **Combattimento → Turno** [1]: each combat belongs to a single turn |
| | | |
| Outcome | It gives the dice the outcome of a fight | **Combattimento → Lancio_Dadi** [2-6]: expresses the minimum and maximum number of darts launched |
|   |   | **Lancio_Dadi → Combattimento** [1..*]: a dice roll is made in a single combat or in N fights |
| | | |
| Third phase | It expresses the play of the third stage of play, moving | **Turno → Spostamento** [0..1]: Moving can be optional or you can only move one |
|   |   | **Spostamento → Turno** [1]: each shift belongs to a single turn |
| | | |
| Action | It expresses the movement of a displaced territory | **Spostamento → Territorio_Occupato** [1]: one shift can be made per shift |
|   |   | **Territorio_Occupato → Spostamento** [0..1]: Moving is optional or is done only once |
| | | |
| Territory membership | It expresses the territorial belonging to the players of a game | **Giocatore → Territorio_Occupato** [0..*]: a player without territories is considered a loser otherwise holds N possible territories |
|   |   | **Territorio_Occupato → Giocatore** [1]: the territory belongs to a single player |
| | | |
| Territory composition | It expresses the structure of a territory where a player resides | **Territorio_Occupato → Territorio** [1]: the territory occupied by a player refers to a territory |
|   |   | **Territorio → Territorio_Occupato** [1]: a territory is necessarily a territory occupied by a player |
| | | |
| Border Composition | It expresses the structure of a boundary related to the territories | **Territorio → Confine** [1..*]: a territory can have N boundaries |
|   |   | **Confine → Territorio** [1]: each boundary is associated with a territory |
| | | |
| Owned territory cards | It expresses the membership of the territory cards to the players of a game | **Giocatore → Carta_Territorio** [0..*]: a losing player does not have any cards. The opposite case may possess N cards |
|   |   | **Carta_Territorio → Giocatore** [0..1]: each card belongs to a player or to anyone (it's in the deck) |
| | | |
| Holds objective cards | It expresses the membership of goal cards to players in a game | **Giocatore → Carta_Obiettivo** [1]: each player has one goal |
|   |   | **Carta_Obiettivo → Giocatore** [0..1]: an objective card or belongs to a player or to anyone (it's in the deck) |
| | | |

# Logical design
## Logical scheme
At this stage of the database design, you get a level of abstraction compared to the previous part.
It will translate the conceptual scheme (already prepared for this purpose through a restructuring) into a logical scheme, depending on the type of data selected, that is the pure relational type.
The relational schemes that follow the primary keys will be indicated in **Bold**,
while the external keys will be indicated in *italic*. If a primary key
is composed of multiple attributes and one or more of these, referring to a key of another table, will come
added {FK} to his side.

| TABLE             | DESCRIPTION                  | 
| ---------------- |----------------------------|
| **PARTITA** | **ID_PARTITA**, NOME_PARTITA, DATA, ID_GAMER_VINC |
| **GIOCATORE** | **ID_GAMER, ID_PARTITA {FK}**, COLORE, NICKNAME, POSIZIONE_TURNO, *ID_CARTA_OBIETTIVO*, N_ARMATE_TOT |
| **ASS_CARTA_TERRITORIO_GIOCATORE** | **ID_GAMER {FK}, ID_CARTA {FK}, ID_PARTITA {FK}** |
| **CARTA_TERRITORIO** | **ID_CARTA**, TERRITORIO, SIMBOLO_CARTA |
| **CARTA_OBIETTIVO** | **ID_CARTA**, DESCRIZIONE |
| **TURNO** | **ID_TURNO, ID_PARTITA{FK}**, *ID_GAMER* |
| **POSIZIONAMENTO_ARMATA** | **ID_POSIZIONAMENTO, ID_PARTITA {FK}, ID_TURNO {FK}**, *ID_TERRITORIO_POS*, RINFORZI_TRUPPE, TIPO_POSIZIONAMENTO |
| **COMBATTIMENTO** | **ID_COMB, ID_TURNO {FK}, ID_PARTITA {FK}**, *ID_TERRITORIO_ATTACCANTE , ID_TERRITORIO_ATTACCATO, ID_G_DIFENSORE*,TIPO_AVANZAMENTO, VINCENTE |
| **LANCIO_DADI** | **N_LANCIO, ID_COMB {FK}, ID_PARTITA {FK}, ID_TURNO {FK}**, DADO1_ATT, DADO2_ATT, DADO3_ATT, DADO1_DIFF, DADO2_DIFF, DADO3_DIFF |
| **SPOSTAMENTO** | **ID_TURNO {FK}, ID_PARTITA {FK}**, *ID_TERRITORIO_PARTENZA, ID_TERRITORIO_ARRIVO*, TRUPPE_SPOSTATE|
| **TERRITORIO_OCCUPATO** | **ID_PARTITA {FK}, ID_TERRITORIO {FK}, GIOCATORE_OCCUPANTE {FK}**, QUANTITA'_TRUPPE |
| **CONFINE** | *ID_TERRITORIO, ID_CONFINANTE* |
| **TERRITORIO** | **ID_TERRITORIO**, NOME_TERRITORIO, ID_CONTINENTE, NOME_CONTINENTE |

# Physical Design
## Implementation Details

Some project implementation details will be modified to make the most of the functionality
of the DBMS Oracle XE 11g. Because Oracle does not implement the boolean type, this has been simulated
with a character (CHAR). If it is necessary instead, represent a value chosen between a set
well defined, you have chosen to use a numeric value.

## Tables Definition
### Table Definition: PARTITA

```sql
/* PARTITA : it contains information about each game and the winning player */
CREATE TABLE PARTITA
(
    /* Attributes */
    ID_PARTITA NUMBER (6) PRIMARY KEY , -- Uniquely identifies each individual game with an increasing number value (not visible to the user)
    NOME_PARTITA VARCHAR2 (30) UNIQUE , -- Introduced to facilitate any searches for a game starting from its name (visible to the user)
    DATA DATE NOT NULL , -- Used for any queries
    ID_GAMER_VINC NUMBER (2) -- Information relating to the id (identification number) of the winning player
);
```

### Table Definition: GIOCATORE

```sql
/* GIOCATORE: contains information relating to the players present in each game with their respective objectives and the "round" to be made */
CREATE TABLE GIOCATORE
(
    
    /* Key attributes */
    ID_GAMER           NUMBER(1) NOT NULL,   -- Numeric identifier (1 to 6) associated with each player in each game
    ID_PARTITA         NUMBER(6) NOT NULL,   -- Reference to ID_PARTITA in the table PARTITA

    /* Attributes */
    COLORE             CHAR(1),              -- Identifies the color chosen by the player's armies. It can be: R (Red), N (Black), G (Yellow), V (Green), B (Blue), U (Purple)
    NICKNAME           VARCHAR2(30),         -- Identifies the nickname chosen by the player
    ID_CARTA_OBIETTIVO NUMBER(6),            -- Reference to ID_CARTA in the table CARTA_OBIETTIVO
    Posizione_Turno    NUMBER(1),            -- It establishes numerically (from 1 to 6 according to the players) the turn/turn of the various players. (editor's note: in particular to whom it belongs)
    N_Armate_Tot       NUMBER(32) DEFAULT 0, -- Information relating to the total armies owned by the player worldwide
    
    /* Constraints */
    CONSTRAINT PK_Giocatore PRIMARY KEY (ID_GAMER,ID_PARTITA) ENABLE,
    CONSTRAINT FK_ID_Partita FOREIGN KEY (ID_PARTITA) REFERENCES PARTITA (ID_PARTITA) ON DELETE CASCADE ENABLE,
    CONSTRAINT FK_ID_Carta_Obiettivo FOREIGN KEY (ID_CARTA_OBIETTIVO) REFERENCES CARTA_OBIETTIVO(ID_CARTA),
    CONSTRAINT Controllo_Colore CHECK (Colore IN('R', 'N', 'G', 'V', 'B', 'U')) ENABLE -- R=Rosso, N=Nero, G=Giallo, V=Verde, B=Blu, U=Viola
);

```

### Table Definition: TURNO

```sql
/* TURNO: it contains information regarding every single turn played by each player in the game. Turn allows you to create an order based on the actions performed */
CREATE TABLE TURNO
(
    /* Key attributes */
    ID_TURNO    NUMBER(6) DEFAULT 0, -- Increasing numerical value that identifies each player's turn. When a turn is 0 (zero) the actions of the preliminary turn are performed (player account, spin choice, card assignment etc ...)
    ID_PARTITA  NUMBER(6) NOT NULL,  -- Reference to ID_PARTITA in the table PARTITA

    /* Attributes */
    ID_GAMER    NUMBER(1) NOT NULL,  -- Reference to ID_GAMER in the table GIOCATORE
 
    /* Constraints */
    CONSTRAINT PK_Turno PRIMARY KEY (ID_TURNO, ID_PARTITA) ENABLE,
    CONSTRAINT FK_Turno_ID_Partita FOREIGN KEY (ID_PARTITA) REFERENCES PARTITA (ID_PARTITA) ON DELETE CASCADE ENABLE,
    CONSTRAINT FK_Turno_ID_Gamer FOREIGN KEY (ID_GAMER,ID_PARTITA) REFERENCES GIOCATORE (ID_GAMER,ID_PARTITA) ON DELETE CASCADE ENABLE
);
```

### Table Definition: CARTA_OBIETTIVO

```sql
/* CARTA_OBIETTIVO: contains information related to objective cards */
CREATE TABLE CARTA_OBIETTIVO
(
    /* Key attributes */
    ID_CARTA    NUMBER(6) NOT NULL,     -- Identify each individual target card

    /* Attributes */
    DESCRIZIONE VARCHAR2(400) NOT NULL, -- Description visible to the player regarding the objective to be completed
    
    /* Constraints */
    CONSTRAINT PK_Carta_Obiettivo PRIMARY KEY (ID_CARTA) ENABLE
);
```

### Table Definition: TERRITORIO

```sql
/* TERRITORIO: contains information related to the territories in the game */
CREATE TABLE TERRITORIO
(
    /* Key attributes */
    ID_TERRITORIO   NUMBER(2) NOT NULL,    -- Numerical value that identifies the territory

    /* Attributes */
    NOME_TERRITORIO VARCHAR2(60) UNIQUE,   -- Name associated with the numeric value of a territory
    ID_CONTINENTE   NUMBER(1) NOT NULL,    -- Numerical value that identifies a continent comprising n ° territories
    NOME_CONTINENTE VARCHAR2(60) NOT NULL, -- Name associated with the numeric value of a territory
   
    /* Constraints */
    CONSTRAINT PK_Territorio PRIMARY KEY (ID_TERRITORIO) ENABLE
);
```

### Table Definition: POSIZIONAMENTO_ARMATA

```sql
/* POSIZIONAMENTO_ARMATA: it contains information relating to the first phase of the game which involves the positioning of tot armies */
CREATE TABLE POSIZIONAMENTO_ARMATA
(
    /* Key attributes */
    ID_POSIZIONAMENTO NUMBER(6) NOT NULL,               -- Increasing numerical value that identifies each individual movement by the players
    ID_TURNO          NUMBER(6) NOT NULL,               -- Reference to ID_TURNO in the table TURNO
    ID_PARTITA        NUMBER(6) NOT NULL,               -- Reference to ID_PARTITA in the table TURNO->PARTITA

    /* Attributes */
    RINFORZI_TRUPPE   NUMBER(3) NOT NULL,               -- Number of troops the player earns after each turn and/or from the bonus card he has available
    TIPO_POSIZIONAMENTO NUMBER(1) NOT NULL,             -- Numeric value indicating the type of positioning made by the player
    
    ID_TERRITORIO_POS NUMBER(2) NOT NULL,
      
    /* Constraints */
    CONSTRAINT PK_Pos_Armata PRIMARY KEY (ID_POSIZIONAMENTO, ID_PARTITA, ID_TURNO) ENABLE,
    CONSTRAINT FK_Pos_Turno FOREIGN KEY (ID_TURNO, ID_PARTITA) REFERENCES TURNO (ID_TURNO, ID_PARTITA) ON DELETE CASCADE ENABLE, -- Default No Action
    CONSTRAINT Controllo_Posizionamento CHECK (TIPO_POSIZIONAMENTO IN('0','1','2','3','4','5','6','7','8')) ENABLE,
    -- Numerical values:
    -- 0 = Insertion of preliminary phase
    -- 1 = Classic insertion
    -- 2 to 8 = Entries derived from the combination of cards chosen (more info in the application documentation)
    CONSTRAINT FK_ID_Territorio_Pos FOREIGN KEY (ID_TERRITORIO_POS) REFERENCES TERRITORIO(ID_TERRITORIO) ENABLE
);
```

### Table Definition: COMBATTIMENTO

```sql
/* COMBATTIMENTO: it contains information related to the second phase of the game that a player can perform in his turn */
CREATE TABLE COMBATTIMENTO
(
    /* Key attributes */
    ID_COMB                  NUMBER(6) NOT NULL,    -- Increasing value associated with each fight made
    ID_TURNO                 NUMBER(6) NOT NULL,    -- Reference to ID_TURNO in the table TURNO
    ID_PARTITA               NUMBER(6) NOT NULL,    -- Reference to ID_PARTITA in the table TURNO->PARTITA
    
    /* Attributes */
    ID_G_DIFENSORE           NUMBER(1) NOT NULL,    -- Reference to ID_GAMER nella tabella GIOCATORE
    VINCENTE                 NUMBER(1) ,            -- 1 if such a fight led to a conquest; 0 if it has been unsuccessful
    
    TIPO_AVANZAMENTO         NUMBER(1) ,            -- 1 If the armies to advance are those equal to the number of dice rolled; 2 If the advancing armies are all the armies present in the territory minus one
    ID_TERRITORIO_ATTACCANTE NUMBER(2) NOT NULL,    -- Reference to ID_TERRITORIO in the table TERRITORIO
    ID_TERRITORIO_ATTACCATO  NUMBER(2) NOT NULL,    -- Reference to ID_TERRITORIO in the table TERRITORIO
    
    /* Constraints */
    CONSTRAINT PK_Combattimento PRIMARY KEY (ID_COMB, ID_TURNO, ID_PARTITA) ENABLE,
    CONSTRAINT FK_Combattimento_Turno FOREIGN KEY (ID_TURNO, ID_PARTITA) REFERENCES TURNO(ID_TURNO, ID_PARTITA) ON DELETE CASCADE ENABLE,
    CONSTRAINT FK_Terr_Attaccante FOREIGN KEY (ID_TERRITORIO_ATTACCANTE) REFERENCES TERRITORIO(ID_TERRITORIO) ENABLE,   -- Default No Action
    CONSTRAINT FK_Terr_Attaccato FOREIGN KEY (ID_TERRITORIO_ATTACCATO) REFERENCES TERRITORIO(ID_TERRITORIO) ENABLE,     -- Default No Action
    CONSTRAINT FK_Difensore FOREIGN KEY (ID_G_DIFENSORE, ID_PARTITA) REFERENCES GIOCATORE(ID_GAMER, ID_PARTITA) ON DELETE CASCADE ENABLE,
    CONSTRAINT TIPO_S CHECK((TIPO_AVANZAMENTO = 1) OR ( TIPO_AVANZAMENTO = 2)), -- 1 If after an attack ONLY armies equal to the dice rolled must be moved; 2 If all the armies in the territory are to be moved.
    CONSTRAINT TIPO_V CHECK((VINCENTE = 0) OR (VINCENTE = 1))
);
```

### Table Definition: LANCIO_DADI

```sql
/* LANCIO DADI: contains information on the dice roll made by the attacking player and defender */  
CREATE TABLE LANCIO_DADI
(
    /* Key attributes */
    ID_COMB       NUMBER(6),           -- Increasing value associated with each fight made
    N_LANCIO      NUMBER(6) NOT NULL,  -- Numerical value used as a technical key together with ID_TURNO, ID_PARTITA 
    ID_TURNO      NUMBER(6) NOT NULL,  -- Reference to ID_TURNO in the table TURNO
    ID_PARTITA    NUMBER(6) NOT NULL,  -- Reference to ID_PARTITA in the table TURNO->PARTITA
    
    /* Attributes */
    DADO1_ATT     NUMBER(1) NOT NULL,  -- Dice 1 used by the attacker (Not null requires that there must be at least one die to be rolled in order to be in the combat/defense phase)
    DADO2_ATT     NUMBER(1),           -- Dice 2 used by the attacker
    DADO3_ATT     NUMBER(1),           -- Dice 3 used by the attacker
    DADO1_DIFF    NUMBER(1) NOT NULL,  -- Dice 1 used by the defender
    DADO2_DIFF    NUMBER(1),           -- Dice 2 used by the defender
    DADO3_DIFF    NUMBER(1),           -- Dice 3 used by the defender
    
    /* Constraints */
    CONSTRAINT PK_Lancio_Dadi PRIMARY KEY (ID_PARTITA, ID_TURNO, ID_COMB, N_LANCIO) ENABLE,
    CONSTRAINT FK_Lancio_Dadi_Comabttimento FOREIGN KEY (ID_TURNO, ID_PARTITA,ID_COMB) REFERENCES COMBATTIMENTO(ID_TURNO, ID_PARTITA,ID_COMB) ON DELETE CASCADE ENABLE, -- Default No Action
    /* A check is made for the maximum and minimum numbers of each die */
    CONSTRAINT Val_Dadi CHECK((DADO1_ATT BETWEEN 1 AND 6) AND (DADO2_ATT BETWEEN 1 AND 6) AND (DADO3_ATT BETWEEN 1 AND 6) AND (DADO1_DIFF BETWEEN 1 AND 6) AND (DADO2_DIFF BETWEEN 1 AND 6) AND (DADO3_DIFF BETWEEN 1 AND 6))
);
   
```

### Table Definition: SPOSTAMENTO

```sql
/* SPOSTAMENTO: it contains information relating to the third phase of the game for the possible movement of armies  */
CREATE TABLE SPOSTAMENTO
(
    /* Key attributes */
    ID_TURNO               NUMBER(6) NOT NULL,    -- Reference to ID_TURNO in the table TURNO
    ID_PARTITA             NUMBER(6) NOT NULL,    -- Reference to ID_PARTITA in the table TURNO->PARTITA

    /* Attributes */
    ID_TERRITORIO_PARTENZA NUMBER(2) NOT NULL,    -- Reference to ID_TERRITORIO in the table TERRITORIO
    ID_TERRITORIO_ARRIVO   NUMBER(2) NOT NULL,    -- Reference to ID_TERRITORIO in the table TERRITORIO
    TRUPPE_SPOSTATE        NUMBER(9) NOT NULL,
    
    /* Constraints */
    CONSTRAINT PK_Spostamento PRIMARY KEY (ID_PARTITA, ID_TURNO) ENABLE,
    CONSTRAINT FK_Spostamento_Turno FOREIGN KEY (ID_TURNO, ID_PARTITA) REFERENCES TURNO(ID_TURNO, ID_PARTITA) ON DELETE CASCADE ENABLE, -- Default No Action
    CONSTRAINT FK_Terr_Partenza FOREIGN KEY (ID_TERRITORIO_PARTENZA) REFERENCES TERRITORIO(ID_TERRITORIO) ENABLE,     -- Default No Action
    CONSTRAINT FK_Terr_Arrivo FOREIGN KEY (ID_TERRITORIO_ARRIVO) REFERENCES TERRITORIO(ID_TERRITORIO) ENABLE         -- Default No Action
);
  
```

### Table Definition: CONFINE

```sql
/* CONFINE: contains information about the boundaries in the game */
CREATE TABLE CONFINE
(
    /* Attributes */
    ID_TERRITORIO NUMBER(2) NOT NULL, -- Reference to ID_TERRITORIO in the TERRITORIO table
    ID_CONFINANTE NUMBER(2) NOT NULL, -- Numerical value associated with each border present
   
    /* Constraints */
    CONSTRAINT FK_ID_Territorio FOREIGN KEY (ID_TERRITORIO) REFERENCES TERRITORIO (ID_TERRITORIO) ENABLE, -- Default No Action
    CONSTRAINT FK_ID_Confinante FOREIGN KEY (ID_CONFINANTE) REFERENCES TERRITORIO (ID_TERRITORIO) ENABLE  -- Default No Action
);
  
```

### Table Definition: CARTA_TERRITORIO

```sql
/* CARTA TERRITORIO: contains information about the territory cards in the game */
CREATE TABLE CARTA_TERRITORIO
(
    /* Key attributes */
    ID_CARTA      NUMBER(6) NOT NULL,   -- Numerical value that identifies each card associated with a territory

    /* Attributes */
    TERRITORIO    VARCHAR2(400) UNIQUE, -- Name of reference to the territory
    SIMBOLO_CARTA CHAR,                 -- Identifies the attribute associated with the card: J (Jolly), O (Objective), F (Infantry), C (Cavalry), A (Artillery)

    /* Constraints */
    CONSTRAINT PK_Carta_Territorio PRIMARY KEY(ID_CARTA) ENABLE,
    CONSTRAINT CartaType CHECK(SIMBOLO_CARTA IN('J', 'O', 'F', 'C', 'A')) ENABLE
);
  
```

### Table Definition: TERRITORIO_OCCUPATO

```sql
/* TERRITORIO OCCUPATO: contains information on the territories occupied by the players of a game */
CREATE TABLE TERRITORIO_OCCUPATO
(
    /* Key attributes */
    ID_TERRITORIO       NUMBER(2) NOT NULL, -- Reference to ID_TERRITORIO in the table TERRITORIO
    GIOCATORE_OCCUPANTE NUMBER(1) NOT NULL, -- Player occupying the territory
    ID_PARTITA          NUMBER(6) NOT NULL, -- Reference to ID_PARTITA in the table GIOCATORE->PARTITA

    /* Attributes */
    QUANTITA_TRUPPE     NUMBER(9) DEFAULT 1,

    /* Constraints */
    CONSTRAINT PK_Giocatore_Territorio PRIMARY KEY (ID_TERRITORIO, GIOCATORE_OCCUPANTE, ID_PARTITA) ENABLE,
    CONSTRAINT FK_PK_Terr FOREIGN KEY(ID_TERRITORIO) REFERENCES TERRITORIO(ID_TERRITORIO) ENABLE,
    CONSTRAINT FK_PK_Giocatore FOREIGN KEY (GIOCATORE_OCCUPANTE, ID_PARTITA) REFERENCES GIOCATORE(ID_GAMER, ID_PARTITA) ON DELETE CASCADE ENABLE
);
```

# Associations implement by tables
## Association Table Definition: Ass_Carta_Terr_Giocatore

This table represents the association between the PLAYER table and the CARTA_TERRITORIO table
of the "One to Many" type. Each player can or may not have cards; these are assigned
only after the first conquest of each turn. In addition, they are used to obtain particular combinations
to get additional wagons. After this action, the cards used for that purpose must
be taken to the player. To avoid redundancies and facilitate the task of controlling combinations of each player's position, he preferred to enter the table's keys **GIOCATORE** and the table
**CARTA_TERRITORIO** in this table.

```sql
/* ASS CARTA TERRITORIO GIOCATORE: contains additional information for optimal search of each card associated with a player */
CREATE TABLE ASS_CARTA_TERRITORIO_GIOCATORE
(
    /* Key attributes */
    ID_CARTA   NUMBER(6) NOT NULL, -- Reference to ID_CARTA in the table CARTA_TERRITORIO
    ID_GAMER   NUMBER(1) NOT NULL, -- Reference to ID_GAMER in the table GIOCATORE
    ID_PARTITA NUMBER(6) NOT NULL, -- Reference to ID_PARTITA in the table GIOCATORE->PARTITA

    /* Constraints */
    CONSTRAINT PK_Ass_C_Terr_g PRIMARY KEY(ID_CARTA,ID_GAMER,ID_PARTITA) ENABLE,
    CONSTRAINT FK_Ass_Carta FOREIGN KEY (ID_GAMER,ID_PARTITA) REFERENCES GIOCATORE (ID_GAMER,ID_PARTITA) ON DELETE CASCADE ENABLE,
    CONSTRAINT FK_Ass_Gamer FOREIGN KEY (ID_CARTA) REFERENCES CARTA_TERRITORIO (ID_CARTA) ON DELETE CASCADE ENABLE
);
```
