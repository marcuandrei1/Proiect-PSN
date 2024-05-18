#Proiect-PSN

----------------------------------------------------------------------------------
-- Company: 
-- Engineer: 
-- 
-- Create Date: 05/07/2024 08:36:21 AM
-- Design Name: 
-- Module Name: Proiect_cod_cifru - Behavioral
-- Project Name: 
-- Target Devices: 
-- Tool Versions: 
-- Description: 
-- 
-- Dependencies: 
-- 
-- Revision:
-- Revision 0.01 - File Created
-- Additional Comments:
-- 
----------------------------------------------------------------------------------


library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
use ieee.std_logic_arith.all;
use ieee.numeric_std.all;
-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity Proiect_cod_cifru is
    port(liber_ocupat, introducere_caractere: out std_logic;       --leduri
        adauga_cifra, reset: in std_logic;                     --buton
        clock:in std_logic;     --clock-ul placii
        UP,DOWN: in std_logic;
        segmente_afisate: out  std_logic_vector (0 to 6);       --cand vrem sa introducem numere sa ne apara fiecare cifra pe 7 segments
        anod: out std_logic_vector (7 downto 0)
        );          
end Proiect_cod_cifru;

architecture Behavioral of Proiect_cod_cifru is
    component Dec_4_la_7_seg is
    port( intrari: in std_logic_vector  (3 downto 0);
          contor: in std_logic_vector(1 downto 0);
          out_seg: out std_logic_vector (0 to 6);
          en: std_logic;
          an: out std_logic_vector (7 downto 0));
    end component;
    
    component MPG is
    Port ( btn : in STD_LOGIC;
           clk : in STD_LOGIC;
           en : out STD_LOGIC);
    end component;

    signal UP_in:std_logic;
    signal DOWN_in:std_logic;
    signal liber_ocupat_in:std_logic := '0';               --am nevoie de semnalul asta pentru ca nu pot scrie IF la liber_ocupat ca e de tip out
    signal introducere_caractere_in: std_logic;
    signal adauga_cifra_in: std_logic :='0';
    signal segmente_afisate_in: std_logic_vector (0 to 6);
    
    signal contor_3: std_logic_vector (1 downto 0);                     --contorul pentru cele 3 cifre introduse
    signal iteratie : std_logic_vector (3 downto 0) := "0000";
    signal enable: std_logic := '0';

begin
    C1: MPG port map (UP,clock,UP_in);              --UP si DOWN sunt acuma clock-uri
    C2: MPG port map (DOWN,clock,DOWN_in);
    C3: MPG port map (adauga_cifra,clock,adauga_cifra_in);
    
    process(adauga_cifra_in,reset,UP_in,DOWN_in)
    
    variable vector_numere: std_logic_vector (2 downto 0);
    
    begin
        if reset = '1' then 
            contor_3 <= "00";       --reseteaza codul introdus pana acum
            liber_ocupat_in <= '0' ;
            introducere_caractere_in <= '0';
            enable <= '0';
            -- de pus iteratie <= '0' ?
        else 
        if liber_ocupat_in = '0' then            --daca e 0 inseamna ca e liber
            if adauga_cifra_in = '1' and rising_edge(clock) then      -- daca apasam butonul sa adaugam cifre atunci se porneste si ledul de introducere cifre
                introducere_caractere_in <= '1';
                iteratie <= "0000";
                enable <= '1';
            end if;
                
            if UP_in= '1' and rising_edge(clock) then    
                    iteratie <= iteratie  + 1;
            end if;
            if DOWN_in= '1' and rising_edge(clock) then    
                    iteratie <= iteratie  - 1;
             end if;
            end if;
            
        end if;
        
        
      
             
    end process;
    C4: Dec_4_la_7_seg port map (iteratie,contor_3,segmente_afisate_in,enable,anod);
    introducere_caractere <= introducere_caractere_in;
    segmente_afisate<=segmente_afisate_in;
end Behavioral;
