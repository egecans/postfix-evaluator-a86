# postfix-evaluator-a86
evaluate the result by given postfix input in a86 assembly language

First of all,  we define a number as a byte type variable with 5 character. Last character is for $ char which is needed for printing string. 
After that, we define cr variable for printing the result at the start of the new line.
Then define temp variable for storing temporary values in next operations 
and counter for checking whether we process all the bytes of printing string at the end.

start label:
move 0 to cx which is used for storing current integer
move 0 to bl which will be used as a counter of indices

morechar label:
move 01 to ah and int 21 for reading a character
then move 0 to dx for cleaning dx then move al (reading char) to dl
then move cx (current int) to ax
after that compare whether the reading character is enter
if it is enter then jumps setupdon1 which jumps to setup_string as a result, 
we couldn't jmp directly to the setup_string label because of the restrictions jmp forward in a86

if it is not enter than check whether it is ' ' (space) or not
if it is space then jump finnumjmp1 which is another dummy label which goes to finnum as a result
if the char is not space then it continues to dolasgel label which is basicly checks what is the char

dolasgel label:
checks what character is (by assuming all given inputs are meaningful)
meaningful inputs should be ' &, +,*,/,0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F,^,|'
it checks the order of ASCII characters
first checks whether it is & or not if it is then goes to andop1 which is a dummylabel going to andop
then checks whether it is * or not if it is then goes to multop1 which is a dummylabel going to multop
then checks whether it is + or not if it is then goes to addop
then checks whether it is / or not if it is then goes to divop1 which is a dummylabel going to divop
then checks whether it is an integer, if it is pass this comparings and now lower than value of ":" then it should be integer (by assuming meaningful inputs) and goes to intop label
after pass these test it checks whether it is hexa letter, if its ASCII value lower than 'G' then it must be hexa then jump to hexa
then if checks whether it is ^ , if it is then goest to xorop1 is a dummylabel going to xorop
LASTLY checks whether it is | , if it is then goest to orop1 is a dummylabel going to orop

don label:
For meaningful inputs it enters here only for hexa digits
Firstly moves dx (reading char) to temp variable
then, move cx (current hexadecimal) to ax
then mov cx to 16 (for convert it hexa type)
then multiply ax with cx and result would be ax as we know in a86 opeartions
then add temp to the ax (reading hexadecimal) and move ax to cx which is current int for now

anadonus label:
jumps morechar label backwardly

intop label:
because before the jump here we subtracted dx from ":" ASCII value first we added that value to dx
then add '0's ASCII value to keep its real value (i.e if it is 8, result will be ASCII(8)-ASCII(0)=8)
then jump to don label which modifies the storing hexadecimal

hexa label:
because before the jump here we subtracted dx from "G" ASCII value first we added that value to dx
then add '7's ASCII value to keep its real value (i.e if it is B, result will be ASCII(B)-ASCII(7)=11)
then jump to don label which modifies the storing hexadecimal

!: because before the operator comes, we push stack the previous hexadecimals pop 2 of them which will be operated all of these op labels

addop label:
because before the operator comes, we push stack the previous hexadecimals pop 2 of them which will be operated 
then add these 2 operands and push the result cx to the stack again.
then clear cx with moving it 0 and jump to anadonus label which jumps morechar again

multop label:
multiply 2 operands which is popped and push the result cx to the stack again.
then clear cx with moving it 0 and jump to anadonus label which jumps morechar again

andop label:
bitwise and 2 operands which is popped and push the result cx to the stack again.
then clear cx with moving it 0 and jump to anadonus label which jumps morechar again

divop label:
First move 0 to dx and clear it to prevent unwanted situations
then divide 2 operands which is popped and push the result cx to the stack again.
then clear cx with moving it 0 and jump to anadonus label which jumps morechar again

xorop label:
bitwise xor 2 operands which is popped and push the result cx to the stack again.
then clear cx with moving it 0 and jump to anadonus label which jumps morechar again

orop label:
bitwise or 2 operands which is popped and push the result cx to the stack again.
then clear cx with moving it 0 and jump to anadonus label which jumps morechar again


finnum label:
when hexadecimal is completed cx (current hexadecimal) comes there
if cx is 0 then goes to morechar again because no need to push somewhere to 0
if it is different from 0 then push the current hexadecimal to stack and jump morechar with anadonus label

setup_string label:
mov bx to adress of numbers + 4 ( like numbers[4] which is 5th one, the last 0)
then move '$' to bx then decrease bx (which is now numbers[3] 4th one, 0 before the last.)

continue with comparing cx with 0 if it is 0 then jump the enternum which is the situations that only entering a 1 hexadecimal, 

enternum label:
it's only enter here when input includes only 1 inputs then push that hexadecimal to stack to print it in the end

afternum label:
pop ax, because we'll operates with ax and convert it hexadecimal string.

convert_decimal label:
Firstly move counter variable to dx (we will check with this whether number is end or not)
then increase dx and moves it to the counter again
then clear dx with moving it 0 to not come across with undesired situations
moves 16 (because we investigate hexadecimals) to cx and div cx which divides ax (popped) value 
then the remainder will be dx and add them 48 to get its real value.
if dx greater than 9 then it will be hexaletter because of that we jump to prthexa

prthexa label:
if it is greater than 9, the value comes there and to make it A,B,C,D,E,F we add dx 7 to get these letters ASCII value. (i.e 48+11 is not equal to B but if you add 7 then it makes B in ASCII)
then jumps gec label

gec label:
move dl to the [bx] (current index of bx) , and decrease bx to next opeartions,
then compare counter with 4 if it is 4 then the number is end and go out to the loop, if it is not 4 then jump convert_decimal again


print_cr label:
print out the newline with the starting of the line

printout labe:
move bx(the String version of the hexadecimal result we get) to dx and print that string out

exit label:
exit code and program ends








