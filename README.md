This project contains a domain specific language (DSL) that allows users to create their own version of the game of life.
It uses Eclipse, Java 11 and Xtext.

Created by Tim Alessie and Susan van den Broek, November 2023.

## Grammar specification
The grammar looks as follows:
- Game = Grid and a list of Rules
- Grid = a list of Cells
- Cell = A Coordinate in combination with a Status
    - A list of Cells is created by defining multiple Cells in a block of code, one Cell per line.
    - A Cell represents a position on the board.
- Coordinate = (X, Y)
- Status = Dead or Alive.
    - The status represents the state that the Cell is in
- Rule = the inputStatus, an Evaluator and Threshold, followed by the corresponding outputStatus
    - the inputStatus is the condition (Dead or Alive) that a Cell has to meet in order for this rule to be applied
    - the Evaluator and Threshold are compared against the number of alive neighbors of the cell.
        - i.e.: Evaluator = <, Threshold = 3. This means that the rule applies whenever there are less than 3 surviving neighbors
    - the outputStatus is the outcome of the rule. A Cell can either become Alive or Dead when the conditions of the rules are satisfied.
- Evaluator = either == (EQUALS), or < (LESS), or > (MORE)
    - Evaluators are used to compare the surviving neighbors of a Cell, to a specified treshold
- Threshold = an integer representing how many surviving neighbors are required for a rule to hold

See gol.models/specs/properGOL.tdsl for a proper working example.

## Running your DSL
The following steps are required to run the language specification that you've written:
- Launch the runtime workspace (contains the folders gol.rules.models and short.life)
- In this workspace, modify the 'spec' file (gol.rules.models/specs/nameOfYourSpecFile.tdsl) according to your specifications
- Clean the project in the runtime workspace, by clicking: project -> clean
- A file will be generated in gol.models/src-gen/RulesOfLife.java. Move it to short.life/src/GameOfLife/. Replace the existing file.
- You can now run short.life/src/GameOfLife/GameOfLife.java as a java application. Here your language will play out once you click 'Game-> Play'.
- Eventhough you can technically draw Alive Cells on the board, they will not be part of the game and therefore they will Die and disappear. The only Cells in the game are those specified by the user in the specification.

## Tools and guidelines to help you write your specification
Some validation rules have be set in place in order to make it easier for users to create their language specifications.
These validation rules are:
- An *error* willl be raised whenever a user specifies rules for the same inputStatus with overlapping domain. This is because rules like these lead to ambiguity. It is unclear what should happen, and it very likely that the user either made a typo, or an error in their judgement. With this rule it is still possible to specify whatever the user would want. This is because you can always define multiple rules using the EQUALS (==) evaluator. That way, all thresholds in the domain can be covered. (i.e., Dead (==1) Alive; Dead (==2) Dead; Dead (==3) Alive; etc..). This rule also filters out any duplicated rules.
    - For instance, the following two rules are not allowed to exist simultainiously:
        - Dead (> 3) Alive
        - Dead (< 7) Dead
- An *error* will be raised whenever a user specifies a rule with a threshold value smaller than 0 or greater than 8. This is because each cell can have at maximum 8 neighbours, and those neighbors can at minimum all be dead.
- A *warning* will be raised whenever a user specifies two cells with the exact same coordinates and the same status. This is most likely an typing error.
- An *error* will be raised whenever a user specifies two cells with the exact same coordinates, but a different status. This is most likely a typing error, and it leads to ambiguity.
- An *error* will be raised whenever a user specifies a cell with negative coordinates. This is because the grammar does not allow minus signs.
Please also keep in mind the following two aspects of the language:
- By default, Cells die in the next interation of the game. This means that unless there is a rule set in place to keep them Alive, they will become Dead.
- By default, Cells are initialted Dead. This means that when defining your Grid, you should mention Cells with an Alive status.

## Some examples
See gol.rules.model/specs/ for some example specifications. They are explained below.
- properGOL.tdsl defines the game of life with the rules as specified by John Conway. We initialised the grid with some known shapes that lead to patterns.
- overlappingDomainError.tdsl shows a specification in which an overlapping domain with same inputStatus error is raised
- thresholdError.tdsl shows a specification in which a treshold error is raised
- duplicateCellWarning shows a specification in which the duplicate cell warning is raised. Uncomment the first Cell the change the warning into an error.
