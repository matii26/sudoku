# sudoku

<Window x:Class="SudokuWPF.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Sudoku" Height="450" Width="450">
    <Grid Name="SudokuGrid">
        <!-- Definicje wierszy i kolumn -->
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="Auto"/>
        </Grid.ColumnDefinitions>

        <!-- Przycisk sprawdzający -->
        <Button Content="Sprawdź" Grid.Row="0" Grid.Column="1" Click="CheckButton_Click"/>

        <!-- Plansza Sudoku -->
        <Grid Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
                <ColumnDefinition Width="Auto"/>
            </Grid.ColumnDefinitions>
            <!-- Komórki Sudoku -->
            <!-- Tutaj dodasz TextBoxy w pętli DrawBoard() -->
        </Grid>
    </Grid>
</Window>


using System;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Input;

namespace SudokuWPF
{
    public partial class MainWindow : Window
    {
        private int[,] board = new int[9, 9];

        public MainWindow()
        {
            InitializeComponent();
            InitializeBoard();
            DrawBoard();
        }

        private void InitializeBoard()
        {
            // Inicjalizacja planszy z wartościami początkowymi
            // Możesz użyć algorytmu do generowania planszy lub załadować z pliku, bazy danych itp.
        }

        private void DrawBoard()
        {
            // Rysowanie planszy Sudoku
            for (int i = 0; i < 9; i++)
            {
                for (int j = 0; j < 9; j++)
                {
                    TextBox textBox = new TextBox
                    {
                        Width = 40,
                        Height = 40,
                        TextAlignment = TextAlignment.Center,
                        FontSize = 20,
                        Margin = new Thickness(5),
                        Text = board[i, j] == 0 ? string.Empty : board[i, j].ToString()
                    };
                    textBox.TextChanged += TextBox_TextChanged;
                    Grid.SetRow(textBox, i);
                    Grid.SetColumn(textBox, j);
                    SudokuGrid.Children.Add(textBox);
                }
            }
        }

        private void TextBox_TextChanged(object sender, TextChangedEventArgs e)
        {
            // Aktualizacja planszy po zmianie wartości w TextBoxie
            TextBox textBox = sender as TextBox;
            if (textBox != null && int.TryParse(textBox.Text, out int value))
            {
                int row = Grid.GetRow(textBox);
                int col = Grid.GetColumn(textBox);
                board[row, col] = value;
            }
        }

        private bool IsValidBoard()
        {
            // Sprawdzanie poprawności planszy
            for (int col = 0; col < 9; col++)
            {
                if (!IsValidColumn(col) || !IsValidRow(col) || !IsValidSquare(col)) return false;
            }
            return true;
        }

        private bool IsValidColumn(int col)
        {
            // Sprawdzanie poprawności kolumny
            bool[] presence = new bool[9];
            for (int row = 0; row < 9; row++)
            {
                if (board[row, col] != 0)
                {
                    if (presence[board[row, col] - 1]) return false;
                    presence[board[row, col] - 1] = true;
                }
            }
            return true;
        }

        private bool IsValidRow(int row)
        {
            // Sprawdzanie poprawności wiersza
            bool[] presence = new bool[9];
            for (int col = 0; col < 9; col++)
            {
                if (board[row, col] != 0)
                {
                    if (presence[board[row, col] - 1]) return false;
                    presence[board[row, col] - 1] = true;
                }
            }
            return true;
        }

        private bool IsValidSquare(int square)
        {
            // Sprawdzanie poprawności kwadratu 3x3
            bool[] presence = new bool[9];
            int startRow = (square / 3) * 3;
            int startCol = (square % 3) * 3;
            for (int row = startRow; row < startRow + 3; row++)
            {
                for (int col = startCol; col < startCol + 3; col++)
                {
                    if (board[row, col] != 0)
                    {
                        if (presence[board[row, col] - 1]) return false;
                        presence[board[row, col] - 1] = true;
                    }
                }
            }
            return true;
        }

        private void CheckButton_Click(object sender, RoutedEventArgs e)
        {
            // Obsługa kliknięcia przycisku "Sprawdź"
            if (IsValidBoard())
                MessageBox.Show("Gratulacje! Rozwiązanie jest poprawne!");
            else
                MessageBox.Show("Niestety, rozwiązanie zawiera błędy.");
        }
    }
}
