<Window x:Class="SudokuGame.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Sudoku" Height="450" Width="450">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid x:Name="sudokuGrid" Margin="10">
            <Grid.Resources>
                <Style TargetType="TextBox">
                    <Setter Property="Width" Value="60" />
                    <Setter Property="Height" Value="60" />
                    <Setter Property="FontSize" Value="20" />
                    <Setter Property="TextAlignment" Value="Center" />
                    <Setter Property="Margin" Value="1" />
                    <Setter Property="Background" Value="White" />
                    <Setter Property="IsReadOnly" Value="True" />
                </Style>
            </Grid.Resources>
        </Grid>

        <Button Grid.Row="1" Content="Solve" Click="Solve_Click" HorizontalAlignment="Center" />
    </Grid>
</Window>



using System;
using System.Collections.Generic;
using System.Windows;
using System.Windows.Controls;

namespace SudokuGame
{
    public partial class MainWindow : Window
    {
        private readonly int[,] sudokuGrid;
        private readonly TextBox[,] textBoxes;

        public MainWindow()
        {
            InitializeComponent();
            sudokuGrid = new int[4, 4];
            textBoxes = new TextBox[4, 4];

            InitializeSudokuGrid();
            GenerateSudoku();
        }

        private void InitializeSudokuGrid()
        {
            for (int row = 0; row < 4; row++)
            {
                for (int col = 0; col < 4; col++)
                {
                    TextBox textBox = new TextBox
                    {
                        Name = $"textBox_{row}_{col}",
                    };

                    sudokuGrid[row, col] = 0;
                    textBox.TextChanged += TextBox_TextChanged;

                    sudokuGrid.Children.Add(textBox);
                    Grid.SetRow(textBox, row);
                    Grid.SetColumn(textBox, col);

                    textBoxes[row, col] = textBox;
                }
            }
        }

        private void GenerateSudoku()
        {
            // Implement Sudoku generation algorithm here
        }

        private bool IsValidPlacement(int row, int col, int value)
        {
            // Check if the value is valid for the given row and column
            for (int i = 0; i < 4; i++)
            {
                if (sudokuGrid[row, i] == value || sudokuGrid[i, col] == value)
                    return false;
            }

            // Check if the value is valid for the 2x2 subgrid
            int subgridStartRow = row - row % 2;
            int subgridStartCol = col - col % 2;
            for (int i = subgridStartRow; i < subgridStartRow + 2; i++)
            {
                for (int j = subgridStartCol; j < subgridStartCol + 2; j++)
                {
                    if (sudokuGrid[i, j] == value)
                        return false;
                }
            }

            return true;
        }

        private void TextBox_TextChanged(object sender, TextChangedEventArgs e)
        {
            TextBox textBox = sender as TextBox;
            int row = Grid.GetRow(textBox);
            int col = Grid.GetColumn(textBox);

            if (!int.TryParse(textBox.Text, out int value) || value < 1 || value > 4)
            {
                textBox.Text = "";
                sudokuGrid[row, col] = 0;
                return;
            }

            if (!IsValidPlacement(row, col, value))
            {
                textBox.Text = "";
                sudokuGrid[row, col] = 0;
                MessageBox.Show("Invalid placement!");
                return;
            }

            sudokuGrid[row, col] = value;
        }

        private void Solve_Click(object sender, RoutedEventArgs e)
        {
            // Implement Sudoku solving algorithm here
        }
    }
}
