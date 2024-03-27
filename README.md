# sudoku

MainWindow.xaml 
<Grid.Resources> <DataTemplate.Triggers> </DataTemplate.Triggers> </Grid.Resources>

    <Grid.RowDefinitions>
        <RowDefinition Height="*" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="Auto" />
    </Grid.ColumnDefinitions>
    <ItemsControl Grid.Row="0" Grid.Column="0" ItemsSource="{Binding Grid}" Margin="1">
        <ItemsControl.ItemsPanel>
            <ItemsPanelTemplate>
                <UniformGrid Rows="4" Columns="4" />
            </ItemsPanelTemplate>
        </ItemsControl.ItemsPanel>
        <ItemsControl.ItemTemplate>
            <DataTemplate>
                <ContentControl Content="{Binding}"
                                ContentTemplate="{StaticResource SudokuCellTemplate}" />
            </DataTemplate>
        </ItemsControl.ItemTemplate>
    </ItemsControl>
    <Button Grid.Row="1" Grid.Column="1" Content="Sprawdź" Click="CheckButton_Click" Margin="5"/>
</Grid>
MainWindow.xaml.cs 
using System; 
using System.Windows;

namespace WpfApp1 { public partial class MainWindow : Window { private SudokuGame sudokuGame;

    public MainWindow()
    {
        InitializeComponent();
        sudokuGame = new SudokuGame();
        DataContext = sudokuGame;
    }

    private void CheckButton_Click(object sender, RoutedEventArgs e)
    {
        if (sudokuGame.IsSolved())
        {
            MessageBox.Show("Gratulacje! Sudoku zostało rozwiązane poprawnie.", "Sudoku", MessageBoxButton.OK, MessageBoxImage.Information);
        }
        else
        {
            MessageBox.Show("Sudoku jest jeszcze niekompletne lub zawiera błędy.", "Sudoku", MessageBoxButton.OK, MessageBoxImage.Error);
        }
    }
}
}

SudokuGame.cs 
using System; using System.Collections.Generic; using System.Collections.ObjectModel; using WpfApp1;

public class SudokuGame { public ObservableCollection Grid { get; private set; }

public SudokuGame()
{
    Grid = new ObservableCollection<SudokuCell>();
    InitializeGrid();
}

public void InitializeGrid()
{
    // Initialize the grid with some values.
    // For a 4x4 Sudoku game, you can hard-code the values in the following way.
    // For larger Sudoku grids, you might need to use a more sophisticated algorithm to generate the grid.
    Grid.Add(new SudokuCell { Value = 1, IsEditable = true });
    Grid.Add(new SudokuCell { Value = 2, IsEditable = true });
    Grid.Add(new SudokuCell { Value = 3, IsEditable = true });
    Grid.Add(new SudokuCell { Value = 4, IsEditable = true });

    // Initialize the remaining cells with zero values.
    for (int i = 0; i < 12; i++)
    {
        if (Grid[i] != null)
        {
            Grid.Add(new SudokuCell { Value = 0, IsEditable = true });
        }
    }
}

public bool IsSolved()
{
    // Sprawdź czy wszystkie komórki mają wartości od 1 do 4
    foreach (var cell in Grid)
    {
        if (cell.Value < 1 || cell.Value > 4)
        {
            return false;
        }
    }
    // Sprawdź czy nie ma powtórzeń w wierszach, kolumnach i grupach
    for (int i = 0; i < 4; i++)
    {
        if (!IsRowValid(i) || !IsColumnValid(i) || !IsGroupValid(i))
        {
            return false;
        }
    }
    return true;
}

private bool IsRowValid(int rowIndex)
{
    HashSet<int> set = new HashSet<int>();
    for (int i = 0; i < 4; i++)
    {
        int value = Grid[rowIndex * 4 + i].Value;
        if (value != 0 && !set.Add(value))
        {
            return false;
        }
    }
    return true;
}

private bool IsColumnValid(int colIndex)
{
    HashSet<int> set = new HashSet<int>();
    for (int i = 0; i < 4; i++)
    {
        int value = Grid[colIndex + i * 4].Value;
        if (value != 0 && !set.Add(value))
        {
            return false;
        }
    }
    return true;
}

private bool IsGroupValid(int groupIndex)
{
    HashSet<int> set = new HashSet<int>();
    int startRow = (groupIndex / 2) * 2;
    int startCol = (groupIndex % 2) * 2;
    for (int i = startRow; i < startRow + 2; i++)
    {
        for (int j = startCol; j < startCol + 2; j++)
        {
            int value = Grid[i * 4 + j].Value;
            if (value != 0 && !set.Add(value))
            {
                return false;
            }
        }
    }
    return true;
}

public void SetValue(int index, int value)
{
    if (Grid[index].IsEditable)
    {
        Grid[index].Value = value;
    }
    else
    {
        throw new InvalidOperationException("Nie możesz zmieniać tej komórki.");
    }
}
}

SudokuCell.cs 
using System; using System.Collections.Generic; using System.Linq; using System.Text; using System.Threading.Tasks;

namespace WpfApp1 { public class SudokuCell { public int Value { get; set; } public bool IsEditable { get; set; } } }
