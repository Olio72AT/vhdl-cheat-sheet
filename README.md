# <div align="center" > VHDL Cheat Sheet</div>
### <div align="right" > By Ismail El Badawy
### <div align="right" > Extended by Oliver Hladik 

# Table of Contents
 * [Libraries](#libraries)
 * [Entities](#entities)
 * [Architecture](#architecture)
 * [Process](#process)
 * [Assignment](#assignment)
 * [Type Conversion](#types)
 * [Operators](#operators)
 * [Test Bench](#testing)
 * [Code snippets](#snippets)


# Libraries

### 0. IEEE
```vhdl
Library ieee;
```

### 1. Standard Logic.
```vhdl
use ieee.std_logic_1164.all;
```

### 2. Math for numerics
```vhdl
use ieee.numeric_std.all;
```

# Entities

### 1. Declaring an entity:
Here we declare an entity with Three ports an input port called `FirstPort` an output port called `SecondPort` they are both ``std_logic``.

A third port is used as bidirectional with 16 bits.

```vhdl
entity EntityName is 
port (
    FirstPort : in std_logic,
    SecondPort : out std_logic,
    ThirdPort : inout std_logic_vector(15 downto 0)
);
end entity;
```

### 2. Generic Entity declaration
Here we declared the entity with two generic integers that can be used throughout the code of the entity.
```vhdl
entity EntityName is
-- generics fit here.
generic (n : integer := 16, m : integer := 32);
port(

);
end entity;
```


# Architecture

### 1. Writing an architecture 
```vhdl
Architecture architecturename of entityname is
begin 

end architecture;
```

### 2. Using intermediary signals
```vhdl
Architecture architecturename of entityname is
-- All signals fit here
Signal wire : std_logic;
Signal bus : std_logic_vector(number downto 0);
begin 

end architecture;
```

### 3. Using an old entity inside an architecture
```vhdl
Architecture architecturename of entityname is
-- Use the entity declaration but instead of the word entity write component.

component componentname is
-- if the component has a generic
generic(n : integer := 16);
port(
    -- port declaration here.
);
end component;

begin

end architecture;
```
You might want to instantiate it.
```vhdl
label : entityname generic map(16) port map(portlist);
-- Ommit generic map if the component is not generic.
```

# Process

A process is executed sequentially rather than the usual concurrent execution.

#### Using process
```vhdl
-- Process lies inside an architecure block
Architecture name of entityname is
begin

process(-- sensitivity list
    clk, reset
)
begin
    -- inside the process block you can use conditions.
    if(reset = '0') then
        output <= '0';
    elsif (reset = '1') then
        -- Something
    else
        -- Something
    end if;
-- Process ends with this.
end process;

end architecture;
```

# Assignment
### 1. Normal Assignment
```vhdl
    B <= '0';
```

### 2. Using `when` 
```vhdl
-- Actual code inside archtiecture
B <= "0000" when enable = '0'
	else "0001" when A = "00"
	else "0010" when A = "01"
	else "0100" when A = "10"
	else "1000" when A = "11";
```

# <div id="types">Type Conversion
<img src="vhdl-type-conversions.png" width="100%"/>

# Operators

#### Mathematical

|Operator   | Function  |
|---|---|
| **  | Power  |
| rem | Remainder |
| mod | Modulus |
| /   | Divide |
| *   | Multiply |
| -   | Subtract |
| +   | Add |
| abs | Absolute value|


#### Logical Operators
|Operator| 
|---|
|and|
|or|
|not|
|nor|
|nand|
|xor|
|xnor|

### Relational Operators
|Operator   | Function  |
|---|---|
| =  | Equal?  |
| /= | Not Equal? |
| <  | Less than |
| >  | Greater than |
| <= | Less than or equal |
| >= | Greater than or equal|



#  <div id="testing"> Test Bench </div>
A test bench is an entity with no ports.

### Assert
```vhdl
Assert(condition) Report "Error message"
Severity error;
```

### Wait For
```vhdl
WAIT FOR 10ns;
```


## Example
```vhdl

entity testbench is
end testbench;

architecture testing of testbench is 
component and2 is 
    port(
        A, B : in std_logic;
        C : out std_logic
    );
end component;
Signal testa, testb, testc : std_logic;
begin
    process 
    begin
        testa <= '0';
        testb <= '0';
        Wait for 10ns;
        assert(testc = '0') report "C must have been 0 when inserting 00"
        Severity error;
    wait;
    end process;
    uut : and2 port map(A => testa, B => testb, C => testc);

```


# <div id="snippets"> Code Snippets </div>

## Register (1 bit)

```vhdl 
-- Do not forget imports.s
entity reg is
port(
	clk, reset, input, enable: in std_logic;
	output : out std_logic);
end entity;

architecture my_arch of reg is 
begin
process (clk, reset)
begin
	if (reset = '1') then
		output <= '0';
	elsif rising_edge(clk) and enable = '1' then
		output <= input;
	end if;
end process;
end architecture;
```

## N-Bits Register
Using the 1 bit register :
```vhdl
entity nreg is
generic ( n : integer := 16);
port(
	clk, reset, enable : in std_logic;
	input : in std_logic_vector(n-1 downto 0);
	output : out std_logic_vector(n-1 downto 0));
end entity;

architecture my_arch of nreg is
component reg is
port(
	clk, reset, input, enable: in std_logic;
	output : out std_logic);
end component;
begin
	loop1 : for i in 0 to n-1 generate 
		fx: reg port map (clk, reset, input(i), enable, output(i));
	end generate;
end architecture;
```

## Tri-state buffer
```vhdl
entity tri_state_buff is 
generic(n : integer := 16);
port(
	A : in std_logic_vector(n-1 downto 0);
	B : out std_logic_vector(n-1 downto 0);
	C : in std_logic);
end entity;

architecture dataflow of tri_state_buff is
begin
	B <= A when C = '1'
	else (others => 'Z');
end architecture;
```

## 1-bit Full Adder
```vhdl
entity FullAdder is
port(
	A, B: in std_logic;
	Cin: in std_logic;
	sum: out std_logic;
	Cout: out std_logic);
end entity;

Architecture dataflow of FullAdder is
begin
	sum <= A xor B xor Cin;
	Cout <= (A and B) or (Cin and (A xor B));
end Architecture;
```

## N-bit Full Adder
Using the 1 bit full adder :
```vhdl
entity NAdder is
generic(n: integer := 8);
port(
	A, B: in std_logic_vector(n-1 downto 0);
	Cin: in std_logic;
	Cout : out std_logic;
	sum : out std_logic_vector(n-1 downto 0));
end entity;

Architecture my_adder_architecture of NAdder is
component FullAdder is
port(
	A, B: in std_logic;
	Cin: in std_logic;
	sum: out std_logic;
	Cout: out std_logic);
end component;
Signal temp: std_logic_vector(n-1 downto 0);
begin
	f0 : FullAdder port map(A(0), B(0), Cin, sum(0), temp(0));
	loop1: for i in 1 to n-1 generate
		carries: FullAdder port map(A(i),B(i), temp(i - 1), sum(i), temp(i));
	end generate;
```

## N-bit 4 to 1 multiplexer
```vhdl

library IEEE;
use IEEE.STD_LOGIC_1164.ALL;



-- Entity declaration for the 4-to-1 multiplexer
entity MUX4to1 is
    Port ( 
           A : in STD_LOGIC;        -- First input
           B : in STD_LOGIC;        -- Second input
           C : in STD_LOGIC;        -- Third input
           D : in STD_LOGIC;        -- Fourth input
           S : in STD_LOGIC_VECTOR(1 downto 0);  -- Select lines
           Y : out STD_LOGIC        -- Output
          );
end MUX4to1;

-- Architecture declaration
architecture Behavioral of MUX4to1 is
begin
    -- Process to determine output based on select lines
    process(A, B, C, D, S)
    begin
        case S is
            when "00" => Y <= A;  -- If select lines are 00, output A
            when "01" => Y <= B;  -- If select lines are 01, output B
            when "10" => Y <= C;  -- If select lines are 10, output C
            when "11" => Y <= D;  -- If select lines are 11, output D
            when others => Y <= '0';  -- Default case (should not happen)
        end case;
    end process;
end Behavioral;
	Cout <= temp(n-1);
end Architecture;
```

## N-bit 1 to 4 demultiplexer
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Entity declaration for the 1-to-4 demultiplexer
entity DEMUX1to4 is
    Port ( 
           D : in STD_LOGIC;        -- Input
           S : in STD_LOGIC_VECTOR(1 downto 0);  -- Select lines
           Y0 : out STD_LOGIC;      -- Output 0
           Y1 : out STD_LOGIC;      -- Output 1
           Y2 : out STD_LOGIC;      -- Output 2
           Y3 : out STD_LOGIC       -- Output 3
          );
end DEMUX1to4;

-- Architecture declaration
architecture Behavioral of DEMUX1to4 is
begin
    -- Process to determine outputs based on select lines
    process(D, S)
    begin
        -- Default all outputs to '0'
        Y0 <= '0';
        Y1 <= '0';
        Y2 <= '0';
        Y3 <= '0';
        
        case S is
            when "00" => Y0 <= D;  -- If select lines are 00, output D to Y0
            when "01" => Y1 <= D;  -- If select lines are 01, output D to Y1
            when "10" => Y2 <= D;  -- If select lines are 10, output D to Y2
            when "11" => Y3 <= D;  -- If select lines are 11, output D to Y3
            when others => null;   -- Default case (should not happen)
        end case;
    end process;
end Behavioral;
