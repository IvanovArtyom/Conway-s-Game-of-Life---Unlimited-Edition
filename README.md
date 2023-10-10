## Description:
Given a 2D array and a number of generations, compute n timesteps of [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

The rules of the game are:

1. Any live cell with fewer than two live neighbours dies, as if caused by underpopulation.
2. Any live cell with more than three live neighbours dies, as if by overcrowding.
3. Any live cell with two or three live neighbours lives on to the next generation.
4. Any dead cell with exactly three live neighbours becomes a live cell.

Each cell's neighborhood is the 8 cells immediately around it (i.e. [Moore Neighborhood](https://en.wikipedia.org/wiki/Moore_neighborhood)). The universe is infinite in both the x and y dimensions and all cells are initially dead - except for those specified in the arguments. The return value should be a 2d array cropped around all of the living cells. (If there are no living cells, then return ```[[]]```.)
### My solution
```C#
using System.Collections.Generic;
using System.Linq;
using System;

public class ConwayLife
{
    public static int[,] GetGeneration(int[,] cells, int generation)
    {
        List<(int, int, bool)> list = GenerateListFrom2DArray(cells);

        for (int i = 0; i < generation; i++)
        {
            List<(int, int, bool)> listWithShell = CreateShellForList(list);
            list = CreateNewGeneration(list, listWithShell);
        }

        return TurnTo2DArray(list);
    }

    public static List<(int, int, bool)> CreateNewGeneration(List<(int, int, bool)> list,
        List<(int, int, bool)> listWithShell)
    {
        List<(int, int, bool)> newGenerationList = new();

        foreach (var cell in listWithShell)
        {
            int neighboursCount = 0;

            foreach (var cell2 in list)
            {
                if (Math.Abs(cell.Item1 - cell2.Item1) <= 1 && Math.Abs(cell.Item2 - cell2.Item2) <= 1)
                {
                    if (cell != cell2)
                        neighboursCount++;
                }
            }

            if (!cell.Item3 && neighboursCount == 3)
                newGenerationList.Add((cell.Item1, cell.Item2, true));

            if (cell.Item3 && (neighboursCount == 2 || neighboursCount == 3))
                newGenerationList.Add(cell);
        }

        return newGenerationList;
    }

    public static List<(int, int, bool)> CreateShellForList(List<(int, int, bool)> list)
    {
        List<(int, int, bool)> shell = new();

        for (int i = 0; i < list.Count; i++)
        {
            int v1 = list[i].Item1;
            int v2 = list[i].Item2;

            shell.Add((v1 - 1, v2 - 1, false));
            shell.Add((v1 - 1, v2, false));
            shell.Add((v1 - 1, v2 + 1, false));
            shell.Add((v1, v2 - 1, false));
            shell.Add((v1, v2 + 1, false));
            shell.Add((v1 + 1, v2 - 1, false));
            shell.Add((v1 + 1, v2, false));
            shell.Add((v1 + 1, v2 + 1, false));
        }

        shell = shell.Distinct().ToList();

        foreach (var cell in list)
            shell.Remove((cell.Item1, cell.Item2, false));

        shell.AddRange(list);

        return shell;
    }

    public static List<(int, int, bool)> GenerateListFrom2DArray(int[,] cells)
    {
        List<(int, int, bool)> generatedList = new();

        for (int i = 0; i < cells.GetLength(0); i++)
        {
            for (int j = 0; j < cells.GetLength(1); j++)
            {
                if (cells[i, j] == 1)
                    generatedList.Add((i, j, true));
            }
        }

        return generatedList;
    }

    public static int[,] TurnTo2DArray(List<(int, int, bool)> listOfCells)
    {
        int minY, minX, maxY, maxX;
        minY = maxY = listOfCells[0].Item1;
        minX = maxX = listOfCells[0].Item2;

        foreach (var cell in listOfCells)
        {
            if (cell.Item1 > maxY)
                maxY = cell.Item1;

            if (cell.Item1 < minY)
                minY = cell.Item1;

            if (cell.Item2 > maxX) 
                maxX = cell.Item2;

            if (cell.Item2 < minX) 
                minX = cell.Item2;
        }

        int[,] resultArr = new int[maxY - minY + 1, maxX - minX + 1];

        foreach (var cell in listOfCells)
            resultArr[cell.Item1 - minY, cell.Item2 - minX] = 1;

        return resultArr;
    }
}
```
