# Technikalia

KB5028997: Instrukcje ręcznej zmiany rozmiaru partycji w celu zainstalowania aktualizacji WinRE
Windows 10 Windows 10, version 1607, all editions Więcej...
Podsumowanie

Firma Microsoft zmieniła sposób aktualizowania komputerów z uruchomionym środowiskiem odzyskiwania systemu Windows (WinRE). Środowisko WinRE zostanie zaktualizowane przy użyciu comiesięcznej aktualizacji zbiorczej. Ta zmiana dotyczy tylko komputerów, które pobierają aktualizacje z usługi Windows Update (WU) i programu Windows Server Update Services (WSUS). Ta zmiana rozpoczyna się 27 czerwca 2023 r. w przypadku aktualizacji zbiorczej systemu Windows 11 w wersji 22H2.  

Niektóre komputery mogą nie mieć partycji odzyskiwania, która jest wystarczająco duża, aby ukończyć tę aktualizację. Z tego powodu aktualizacja środowiska WinRE może zakończyć się niepowodzeniem. Zostanie wyświetlony komunikat o błędzie „Obsługa środowiska odzyskiwania systemu Windows nie powiodła się”. Aby ułatwić odzyskiwanie po tym błędzie, ten artykuł zawiera instrukcje dotyczące ręcznego zmieniania rozmiaru partycji odzyskiwania, jeśli zostanie wyświetlony systemowy element ErrorPhase o wartości 2. Wymaga to, aby urządzenie miało partycję odzyskiwania po partycji systemu operacyjnego. Wykonaj poniższe kroki, aby to zweryfikować.    
Ręczne zmienianie rozmiaru partycji o 250 MB

    Otwórz okno wiersza polecenia (cmd) jako administrator.

    Aby sprawdzić stan winRE, uruchom reagentc /info. Jeśli środowisko WinRE jest zainstalowane, powinna istnieć „lokalizacja środowiska odzyskiwania systemu Windows” ze ścieżką do katalogu WinRE. Przykład: „lokalizacja środowiska odzyskiwania systemu Windows: [file://%3f/GLOBALROOT/device/harddisk0/partition4/Recovery/WindowsRE]\\?\GLOBALROOT\device\harddisk0\partition4\Recovery\WindowsRE.” W tym miejscu liczba występująca po wartości „dysk twardy” i „partycja” jest indeksem dysku i partycja WinRE jest włączona.

    Aby wyłączyć funkcję WinRE, uruchom 
	----------------------
	reagentc /disable
	----------------------
    Zmniejsz partycję systemu operacyjnego i przygotuj dysk do nowej partycji odzyskiwania.
    Aby zmniejszyć system operacyjny, uruchom 
    ---------------
    diskpart
    ---------------

        Uruchom 

	------------------
	list disk
	------------------

        Aby wybrać dysk systemu operacyjnego, uruchom 

	----------------------------
	sel disk<OS disk index>		Indeks dysku powinien być taki sam jak indeks WinRE.
	----------------------------

        Aby sprawdzić partycję na dysku systemu operacyjnego i znaleźć partycję systemu operacyjnego, uruchom 
  --------------------------      
  list part
  --------------------------
        Aby wybrać partycję systemu operacyjnego, uruchom 
	---------------------------------
	sel part<OS partition index>
	---------------------------------
        Uruchom 
	---------------------------------------------
	shrink desired=250 minimum=250
	---------------------------------------------
        Aby wybrać partycję WinRE, uruchom 
	---------------------------------------------
	sel part<WinRE partition index>
	---------------------------------------------
        Aby usunąć partycję WinRE, uruchom 
	---------------------------------------
	delete partition override
	---------------------------------------
    Utwórz nową partycję odzyskiwania.
        Najpierw sprawdź, czy styl partycji dysku to tabela partycji GUID (GPT), czy główny rekord rozruchowy (MBR).  Aby to zrobić, uruchom 
	--------------------------
	list disk 
	--------------------------
	     Sprawdź, czy w kolumnie „Gpt” znajduje się znak gwiazdki (*).  Jeśli istnieje znak gwiazdki (*), dysk ma format GPT. W przeciwnym razie dysk ma format MBR.
       Jeśli dysk jest gpt, uruchom 
	----------------------------------------------------------------------------
	create partition primary id=de94bba4-06d1-4d40-a16a-bfd50179d6ac 	
	----------------------------------------------------------------------------
       a następnie polecenie 
 	----------------------------------------------------------------------------
	gpt attributes =0x8000000000000001
	----------------------------------------------------------------------------
            Jeśli dysk jest MBR, uruchom 
	-----------------------------------------------------
	create partition primary id=27
	-----------------------------------------------------
        Aby sformatować partycję, uruchom
	----------------------------------------------------
	format quick fs=ntfs label=”Windows RE tools”
	----------------------------------------------------
    Aby potwierdzić, że partycja WinRE została utworzona, uruchom 
	------------------------
	list vol
	------------------------
    Aby wyjść z części dyskowej, uruchom 
	----------------
	exit
	----------------
    Aby ponownie włączyć funkcję WinRE, uruchom 
	--------------------------------------
	reagentc /enable
	--------------------------------------
    Aby sprawdzić, gdzie jest zainstalowany składnik WinRE, uruchom 
	----------------------
	reagentc /info
	----------------------
Uwaga Jeśli tworzenie nie powiodło się lub nie chcesz rozszerzać partycji WinRE, uruchom 
------------------------------
reagentc /enable
------------------------------
aby ponownie włączyć funkcję WinRE.
