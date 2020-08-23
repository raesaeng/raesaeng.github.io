### sed (Stream Editor)

* 특징
  * 입력받은 내용을 그대로 출력한다.
  * 패턴 검색 등으르 통해 특정 라인에 대한 편집이 가능하다.
  * 비 대화형 편집기이다. (대화형 편집기는 vi)
  * 파일 입력시 파일 내용이 변경되지 않는다.
  * 라인 단위로 처리된다.
* 패턴 스페이스
  * 라인 단위로 처리할 때 패턴 스페이스에 집어넣고 처리한다.
  * 패턴 스페이스에서 처리한 후 표준 출력으로 내보낸다.
  * 옵션을 사용해서 출력을 억제할 수 있다.
* 홀딩 버퍼
  * 패턴 스페이스에서 특정 라인을 지정하여 저장하는 버퍼
  * G 옵션 사용으로 출력 가능하다.
* 어드레스
  
* 주소, 라인 수를 의미한다.
  
* sed 명령어 형식

  ```
  # sed [옵션] '[범위] [명령]' [대상파일]
  # [명령어] | sed [옵션] '[범위] [명령]'
  ```

  * 명령어

    * p : 해당 라인을 표준 출력으로 내보낸다.
    * d : 해당 라인을 삭제한다.
    * s : 교체 (Substitute). 패턴을 찾고, 찾은 패턴을 변경한다.
    * q : 패턴에 해당되는 라인에서 sed을 종료한다.
    * r : 파일에서 내용을 읽어온 후 스트림에 삽입한다.
    * a\ : 새로운 라인을 추가한다. (아래) append
    * i\ : 새로운 라인을 추가한다. (위) insert
    * c\ : 해당 라인의 내용을 변경한다. change
    * w : 파일로 저장된다.
    * ! : not. 해당되지 않는 패턴을 검색한다.

  * 옵션

    * -e : 다중실행
    * -f : 스크립트를 불러와서 sed를 실행하기
    * -n : 기본 출력 억제

  * 교체플래그

    * g : s와 함께 사용. g플래그 미사용시 s명령어로 패턴 교체시 첫번째 패턴만 교체되고 이후의 패턴은 건너뜀.

      ```
      s/Mary/Jand/g
      ```

  * 범위지정

    1. n, m : n줄부터 m줄까지

       50,60 : 50번째 줄부터 60번째 줄까지

       ```
       sed -n '50,60p' datafile
       sed '1,5d' datafile
       ```

    2. 패턴 검색 : /정규표현식/

       ```
       sed -n '/a..e?/p' datafile
       ```

    3. 패턴으로 범위지정 : /패턴1/, /패턴2/

       ```
       sed -n '/3/,/5/p' line
       ```

* Sed Print Command

  * 기본적으로 전체를 출력하면서  north가 있는 패턴은 한번 더 출력

    ```
    student@CCCR03:~/Shell$ sed '/north/p' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * 기본적으로 전체를 출력하고 1~5줄을 한번 더 출력

    ```
    student@CCCR03:~/Shell$ sed '1,5p' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * 기본 출력을 억제하고 north 패턴이 있는 라인만 출력

    ```
    student@CCCR03:~/Shell$ sed -n '/north/p' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    ```

  * 기본 출력을 억제하고 1~5줄만 출력

    (grep과 별다를거 없는  출력들.)

    ```
    student@CCCR03:~/Shell$ sed -n '1,5p' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    ```

  * west 패턴부터 시작해서 east 패턴 찾을 때까지 출력

    ```
    student@CCCR03:~/Shell$ sed -n '/west/,/east/p' datafile2
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    ```

  * 5번째 줄부터 ^northeast 패턴 찾을 때까지 출력

    ```
    student@CCCR03:~/Shell$ sed -n '5,/^northeast/p' datafile
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    ```

  * 전체를 출력하며 north 패턴을 찾지 못한 라인을 한번 더 출력

    ```
    student@CCCR03:~/Shell$ sed '/north/!p' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

    

* Sed Delete Command

  * 3번 라인만 삭제한 후 나머지 출력

    ```
    student@CCCR03:~/Shell$ sed '3d' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * north가 포함된 라인만 삭제한 후 나머지 출력

    ```
    student@CCCR03:~/Shell$ sed '/north/d' datafile
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * 2번 라인부터 5번 라인까지 삭제한 후 나머지 출력

    ```
    student@CCCR03:~/Shell$ sed '2,5d' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * north가 포함된 라인부터 south가 포함된 라인까지 삭제한 후 나머지 출력

    ```
    student@CCCR03:~/Shell$ sed '/north/,/south/d' datafile
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    ```



* Sed Subtitute Command

  * 줄 첫번째 3이 A로 교체

    ```
    student@CCCR03:~/Shell$ sed 's/3/A/' datafile
    northwest	NW	Charles Main		A.0	.98	3	34
    western		WE	Sharon Gray		5.A	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	A	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	1A
    ```

  * 줄 전체 (g)의 3이 A로 교체

    ```
    Gstudent@CCCR03:~/Shell$ sed 's/3/A/g' datafile
    northwest	NW	Charles Main		A.0	.98	A	A4
    western		WE	Sharon Gray		5.A	.97	5	2A
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	A	1A
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	1A
    ```

  * 줄 전체 (g) 의 west가 north 로 교체한 후 표시

    ```
    student@CCCR03:~/Shell$ sed 's/west/north/g' datafile
    northnorth	NW	Charles Main		3.0	.98	3	34
    northern		WE	Sharon Gray		5.3	.97	5	23
    southnorth	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * west로 시작하는 줄에서 첫 west를 north으로 교체

    ```
    student@CCCR03:~/Shell$ sed -n 's/west/north/gp' datafile
    northnorth	NW	Charles Main		3.0	.98	3	34
    northern		WE	Sharon Gray		5.3	.97	5	23
    southnorth	SW	Lewis Dalsass		2.7	.8	2	18
    ```

  * 두 개의 숫자로 끝나는 줄은 ",5" 를 추가하여 표시y

    ```
    student@CCCR03:~/Shell$ sed 's/[0-9][0-9]$/%,5/' datafile
    northwest	NW	Charles Main		3.0	.98	3	%,5
    western		WE	Sharon Gray		5.3	.97	5	%,5
    southwest	SW	Lewis Dalsass		2.7	.8	2	%,5
    southern	SO	Suan Chin		5.1	.95	4	%,5
    southeast 	SE	Patricia Hemenway	4.0	.7	4	%,5
    eastern		EA	TB Savage		4.4	.84	5	%,5
    northeast 	NE	AM Main Jr.		5.1	.94	3	%,5
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	%,5
    ```

  * Hemenway를 Jones으로 교체한  표시

    ```
    student@CCCR03:~/Shell$ sed -n 's/Hemenway/Jones/gp' datafile
    southeast 	SE	Patricia Jones	4.0	.7	4	17
    ```

  * 'd'는 해당라인을 삭제하는 기능, 반면 's'는 내부단어 삭제 가능

    ```
    student@CCCR03:~/Shell$ sed 's/[0-9]$//' datafile
    northwest	NW	Charles Main		3.0	.98	3	3
    western		WE	Sharon Gray		5.3	.97	5	2
    southwest	SW	Lewis Dalsass		2.7	.8	2	1
    southern	SO	Suan Chin		5.1	.95	4	1
    southeast 	SE	Patricia Hemenway	4.0	.7	4	1
    eastern		EA	TB Savage		4.4	.84	5	2
    northeast 	NE	AM Main Jr.		5.1	.94	3	1
    north		NO	Margot Weber		4.5	.89	5	 
    central		CT 	Ann Stephens		5.7	.94	5	1
    ```

  * 독립된 단어 형태의 숫자로 끝나는 단어 삭제

    ```
    student@CCCR03:~/Shell$ sed 's/\<[0-9]\>$//' datafile
    northwest	NW	Charles Main	3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * a\ : 새로운 라인을 추가 (아래) append

    ```
    # sed '10a\
    10줄 아래에 추가됩니다.' datafile
    
    student@CCCR03:~/Shell$ sed '/north/ a\
    > *** North Region Report ***' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    *** North Region Report ***
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    *** North Region Report ***
    north		NO	Margot Weber		4.5	.89	5	 9
    *** North Region Report ***
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * i\ : 새로운 라인을 추가 (위) insert

    ```
    # sed '1i\
    제목입니다' datafile
    
    student@CCCR03:~/Shell$ sed '/eastern/ i\
    > NEW ENGLAND REGION' datafile
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    NEW ENGLAND REGION
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```

  * c\ : 해당 라인의 내용을 변경. change

    ```
    # sed '/northeast/c\'
    northeast는 폐선입니다.' datafile
    
    student@CCCR03:~/Shell$ sed '/west/c\
    > *** West Region now Closed ***' datafile
    *** West Region now Closed ***
    *** West Region now Closed ***grep '
    *** West Region now Closed ***
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    north		NO	Margot Weber		4.5	.89	5	 9
    central		CT 	Ann Stephens		5.7	.94	5	13
    ```



* Sed File Input/Output

  * r : 파일의 내용을 읽어와서 찾은 부분에 삽입한다.
  
    message 파일의 내용을 eastern 패턴 아래에 삽입.
  
    ```
    sed '/eastern/r message' datafile
    ```
  
  * w : 찾은 내용을 파일로 저장.
  
    ```
    sed '/north/w northfile' datafile
    = grep 'north' datafile > northfile
    = sed -n '/north/p' datafile > northfile
    ```
  
    

* Sed 다중 편집

  ```
  sed -e '[작업1]' -e '[작업2]' [대상파일]
  ```

  다중 실행 시 앞의 명령어의 실행결과가 뒤 명령어의 실행결과에 영향을 줄 수 있으므로 영향을 파악한 뒤 실행한다.

  ```
  # cat > fruits
  orange
  apple
  banana
  
  orange -> apple
  apple -> banana
  
  apple
  banana
  banana
  
  #sed -e 's/apple/orange/' -e 's/orange/banana/' fruits
  banana
  banana
  banana
  ```

  

* Sed 스크립트

  복잡한  sed 실행 내용을 별도의 파일로 저장하고 파일을 불러와서 실행한다.

  * 제목 붙이기 + 꼬리말 붙이기 + 중간 sed 내용 변경 등의 동일한 작업 반복시 실행내용을 스크립트 파일에 저장해 놓고 계속해서 사용 할 수 있다.

    ```
    # My First Script
    /Yesterday/ i\
    Yesterday - Beetles
    /She/ i\
    She's Gone - Steelheart
    $a\
    End
    
    # cat song
    Yesterday All my troubles seems so far away
    She's Gone... Out of my life...
    
    # sed -f sedscript song
    Yesterday - Beetles
    Yesterday All my troubles seems so far away
    She's Gone - Steelheart
    She's Gone... Out of my life...
    End
    
    ```

    ```
    student@CCCR03:~/Shell$ vi adding 
    student@CCCR03:~/Shell$ cat adding 
    /Lewis/ a\
    Lewis is the TOP Salesperson for Apfil!\
    Lewis is moving to the southen district next month.\
    CONGRATULATIONS!
    /Margot/c\
    ****************************\
    ****MARGOT HAS RETIERED ****\
    ****************************
    1i\
    EMPLOYEE DATABASE
    $d
    
    student@CCCR03:~/Shell$ sed -f adding datafile
    EMPLOYEE DATABASE
    northwest	NW	Charles Main		3.0	.98	3	34
    western		WE	Sharon Gray		5.3	.97	5	23
    southwest	SW	Lewis Dalsass		2.7	.8	2	18
    Lewis is the TOP Salesperson for Apfil!
    Lewis is moving to the southen district next month.
    CONGRATULATIONS!
    southern	SO	Suan Chin		5.1	.95	4	15
    southeast 	SE	Patricia Hemenway	4.0	.7	4	17
    eastern		EA	TB Savage		4.4	.84	5	20
    northeast 	NE	AM Main Jr.		5.1	.94	3	13
    ****************************
    ****MARGOT HAS RETIERED ****
    ****************************
    ```



* sed 실습 예제

  ```
  sed 실습
  시나리오 (databook 파일)
  1.  Jon의 이름을 Jonathan으로 교체하세오
  student@CCCR03:~/Shell$ sed -i 's/Jon/Jonathan/' databook
  student@CCCR03:~/Shell$ sed -n 's/Jon/Jonathan/' databook
  
  2. 처음 세 행을 삭제 하시오
  student@CCCR03:~/Shell$ sed '1,3d' databook
  
  3. Lane이 포함된 행을 삭제하시오
  student@CCCR03:~/Shell$ sed '/Lane/d' databook
  
  4. 생일이 November나 December인 사람들의 행을 출력하시오
  
  student@CCCR03:~/Shell$ sed -n '/:1[12]\//p' databook
  Steve Blenheim:238-923-7366:95 Latham Lane, Easton, PA 83755:11/12/56:20300
  Karen Evich:284-758-2867:23 Edgecliff Place, Lincoln, NB 92743:11/3/35:58200
  Karen Evich:284-758-2867:23 Edgecliff Place, Lincoln, NB 92743:11/3/35:58200
  James Ikeda:834-938-8376:23445 Aster Ave., Allentown, NJ 83745:12/1/38:45000
  Barbara Kertz:385-573-8326:832 Ponce Drive, Gary, IN 83756:12/1/46:268500
  
  5. Fred로 시작하는 행의 끝에 세 개의 별표(*)를 붙이시오
  
  student@CCCR03:~/Shell$ grep Fred databook
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900
  student@CCCR03:~/Shell$ sed -n '/Fred/s/$/***/p' databook
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900***
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900***
  
  
  6. Popeye의 생일을 11/14/46으로 교체하시오
  
  student@CCCR03:~/Shell$ sed 's/3\/19\/35/11\/14\/45/p' databook
  student@CCCR03:~/Shell$ sed -n '/Popeye/s/1?[0-9]\/[1-3]?[0-9]\/[0-9][0-9]/11\/14\/46/p' databook
  
  
  7. 빈행을 삭제하시오
  
  student@CCCR03:~/Shell$ sed '/^$/d' databook
  Steve Blenheim:238-923-7366:95 Latham Lane, Easton, PA 83755:11/12/56:20300
  Betty Boop:245-836-8357:635 Cutesy Lane, Hollywood, CA 91464:6/23/23:14500
  Igor Chevsky:385-375-8395:3567 Populus Place, Caldwell, NJ 23875:6/18/68:23400
  Norma Corder:397-857-2735:74 Pine Street, Dearborn, MI 23874:3/28/45:245700
  Jennifer Cowan:548-834-2348:583 Laurel Ave., Kingsville, TX 83745:10/1/35:58900
  Jonathan DeLoach:408-253-3122:123 Park St., San Jose, CA 04086:7/25/53:85100
  Karen Evich:284-758-2857:23 Edgecliff Place, Lincoln, NB 92086:7/25/53:85100
  Karen Evich:284-758-2867:23 Edgecliff Place, Lincoln, NB 92743:11/3/35:58200
  Karen Evich:284-758-2867:23 Edgecliff Place, Lincoln, NB 92743:11/3/35:58200
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900
  Lori Gortz:327-832-5728:3465 Mirlo Street, Peabody, MA 34756:10/2/65:35200
  Paco Gutierrez:835-365-1284:454 Easy Street, Decatur, IL 75732:2/28/53:123500
  Ephram Hardy:293-259-5395:235 CarltonLane, Joliet, IL 73858:8/12/20:56700
  James Ikeda:834-938-8376:23445 Aster Ave., Allentown, NJ 83745:12/1/38:45000
  Barbara Kertz:385-573-8326:832 Ponce Drive, Gary, IN 83756:12/1/46:268500
  Lesley Kirstin:408-456-1234:4 Harvard Square, Boston, MA 02133:4/22/62:52600
  William Kopf:846-836-2837:6937 Ware Road, Milton, PA 93756:9/21/46:43500
  Sir Lancelot:837-835-8257:474 Camelot Boulevard, Bath, WY 28356:5/13/69:24500
  Jesse Neal:408-233-8971:45 Rose Terrace, San Francisco, CA 92303:2/3/36:25000
  Zippy Pinhead:834-823-8319:2356 Bizarro Ave., Farmount, IL 84357:1/1/67:89500
  Arthur Putie:923-835-8745:23 Wimp Lane, Kensington, DL 38758:8/31/69:126000
  Popeye Sailor:156-454-3322:945 Bluto Street, Anywhere, USA 29358:3/19/35:22350
  Jose Santiago:385-898-8357:38 Fife Way, Abilene, TX 39673:1/5/58:95600
  Tommy Savage:408-724-0140:1222 Oxbow Court, Sunnyvale, CA 94087:5/19/66:34200
  Yukio Takeshida:387-827-1095:13 Uno Lane, Ashville, NC 23556:7/1/29:57000
  Vinh Tranh:438-910-7449:8235 Maple Street, Wilmington, VM 29085:9/23/63:68900
  
  8. 아래와 같은 sed 스크립트를 작성하시오
  	첫 줄에 Personal File  제목 삽입
  	San Francisco에 거주하는 사람을 제거		
  	마지막 줄에 THE END 추가
  
  student@CCCR03:~/Shell$ cat sed.script 
  1i\
  Personal File\
  ==============
  /San Francisco/d
  $a\
  ==============\
  THE END
  student@CCCR03:~/Shell$ sed -f sed.script databook 
  Personal File
  ==============
  Steve Blenheim:238-923-7366:95 Latham Lane, Easton, PA 83755:11/12/56:20300
  Betty Boop:245-836-8357:635 Cutesy Lane, Hollywood, CA 91464:6/23/23:14500
  Igor Chevsky:385-375-8395:3567 Populus Place, Caldwell, NJ 23875:6/18/68:23400
  Norma Corder:397-857-2735:74 Pine Street, Dearborn, MI 23874:3/28/45:245700
  
  Jennifer Cowan:548-834-2348:583 Laurel Ave., Kingsville, TX 83745:10/1/35:58900
  Jonathan DeLoach:408-253-3122:123 Park St., San Jose, CA 04086:7/25/53:85100
  Karen Evich:284-758-2857:23 Edgecliff Place, Lincoln, NB 92086:7/25/53:85100
  Karen Evich:284-758-2867:23 Edgecliff Place, Lincoln, NB 92743:11/3/35:58200
  
  Karen Evich:284-758-2867:23 Edgecliff Place, Lincoln, NB 92743:11/3/35:58200
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900
  Fred Fardbarkle:674-843-1385:20 Parak Lane, DeLuth, MN 23850:4/12/23:780900
  Lori Gortz:327-832-5728:3465 Mirlo Street, Peabody, MA 34756:10/2/65:35200
  
  
  Paco Gutierrez:835-365-1284:454 Easy Street, Decatur, IL 75732:2/28/53:123500
  Ephram Hardy:293-259-5395:235 CarltonLane, Joliet, IL 73858:8/12/20:56700
  James Ikeda:834-938-8376:23445 Aster Ave., Allentown, NJ 83745:12/1/38:45000
  Barbara Kertz:385-573-8326:832 Ponce Drive, Gary, IN 83756:12/1/46:268500
  Lesley Kirstin:408-456-1234:4 Harvard Square, Boston, MA 02133:4/22/62:52600
  
  William Kopf:846-836-2837:6937 Ware Road, Milton, PA 93756:9/21/46:43500
  Sir Lancelot:837-835-8257:474 Camelot Boulevard, Bath, WY 28356:5/13/69:24500
  Zippy Pinhead:834-823-8319:2356 Bizarro Ave., Farmount, IL 84357:1/1/67:89500
  
  Arthur Putie:923-835-8745:23 Wimp Lane, Kensington, DL 38758:8/31/69:126000
  Popeye Sailor:156-454-3322:945 Bluto Street, Anywhere, USA 29358:3/19/35:22350
  
  Jose Santiago:385-898-8357:38 Fife Way, Abilene, TX 39673:1/5/58:95600
  Tommy Savage:408-724-0140:1222 Oxbow Court, Sunnyvale, CA 94087:5/19/66:34200
  
  Yukio Takeshida:387-827-1095:13 Uno Lane, Ashville, NC 23556:7/1/29:57000
  Vinh Tranh:438-910-7449:8235 Maple Street, Wilmington, VM 29085:9/23/63:68900
  ==============
  THE END
  ```

  