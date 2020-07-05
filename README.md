# Npgsql-Exercise1
introduction to Npgsql

using System;
using Npgsql;
namespace ActiveRecord
    {
       public class Program
       {
            static void Main(string[] args)
            {
               // Specify connection options and open an connection		
              string connString = System.Configuration.ConfigurationManager.ConnectionStrings["Rental"].ToString();//create variable and storein conn
              NpgsqlConnection conn = new NpgsqlConnection (connString);
                conn.Open();

                 // Define a query
                NpgsqlCommand cmd = new NpgsqlCommand("SELECT title FROM movies", conn);

                // Execute a query
                NpgsqlDataReader dataReader = cmd.ExecuteReader();

                // Read all rows and output the first column in each row
                while (dataReader.Read())
                    Console.Write($"title: {dataReader[0]}\n");
                // Once we're done reading data we need to close the reader
                dataReader.Close();
            
                // NpgsqlCommand is also disposable, so we can use it in using block
                using (var cmd2 = new NpgsqlCommand("DELETE FROM movies WHERE year < 1950", conn))
                {
                    // DELETE statements are not returning any data, so we execute them as NonQuery
                    cmd2.ExecuteNonQuery();
                 
                }

                using (var cmd2 = new NpgsqlCommand("SELECT title, year FROM movies", conn))
                {

                    using (NpgsqlDataReader dataReader2 = cmd2.ExecuteReader())
                    {
                            while (dataReader2.Read())
                            // We can access row fields either by column index or column name
                            Console.Write($"Movie {dataReader2["title"]} was produced in {dataReader2["year"]}\n");
                    }
                }

                Console.Write("Display movies produced in year: ");
                string year = Console.ReadLine();
                //cmd.Parameters.AddWithValue("@year", year);correct way to parse user input
                // This is a bad way of introducing parameters to queries. This is in fact SQL Injection vulnerability!
                // Never write this type of code!!!
                using (var cmd2 = new NpgsqlCommand($"SELECT title, year FROM movies WHERE year = {year}", conn))
                {
                     
                    using (NpgsqlDataReader dataReader2 = cmd2.ExecuteReader())
                    {
                        Console.WriteLine($"Movies produced in {year}");
                        while (dataReader2.Read())
                            Console.Write($"Movie {dataReader2["title"]} was produced in {dataReader2["year"]}\n");
                    }
                }

                // Instead do this properly, by introducing Parameters to sql commands.
                using (var cmd2 = new NpgsqlCommand("SELECT title, year FROM movies WHERE year = @year", conn))
                {
                    cmd2.Parameters.AddWithValue("@year", int.Parse(year));
                    using (NpgsqlDataReader dataReader2 = cmd2.ExecuteReader())
                    {
                        Console.WriteLine($"Movies produced in {year}");

                        while (dataReader2.Read())
                            Console.Write($"Movie {dataReader2["title"]} was produced in {dataReader2["year"]}\n");
                    }
                }
                    // for movie_id input no (3)
                Console.Write("please enter the movie_id:");
                string movie_id = Console.ReadLine();
                using (var cmd2 = new NpgsqlCommand("SELECT year, age_restriction FROM movies WHERE movie_id = @movie_id", conn))
                {
                    cmd2.Parameters.AddWithValue("@movie_id", int.Parse(movie_id));
                    using (NpgsqlDataReader dataReader2 = cmd2.ExecuteReader())
                    {
                     Console.WriteLine($"Movie_id  with {movie_id}");
                     while (dataReader2.Read())
                     {
                        Console.Write($"  : has age restriction of {dataReader2["age_restriction"]}\n");
                        //Console.Write($"Copies of Movie_id {dataReader2["movie_id"]}includes {dataReader2["Copies"]}\n");
                     }
                    }

                }
                    // no(4)
              using (var cmd2 = new NpgsqlCommand("SELECT first_name  FROM actors ", conn))
              {
                //cmd2.Parameters.AddWithValue("@movie_id", int.Parse(movie_id));
                using (NpgsqlDataReader dataReader2 = cmd2.ExecuteReader())
                {

                    while (dataReader2.Read())
                    {
                        //Console.Write($"title: {dataReader[0]}\n");
                        Console.Write($"Actors : {dataReader2[0] }\n");
                    }
                }

              }


                using (var cmd2 = new NpgsqlCommand("SELECT copy_id  FROM copies ", conn))
                {
                    //cmd2.Parameters.AddWithValue("@movie_id", int.Parse(movie_id));
                using (NpgsqlDataReader dataReader2 = cmd2.ExecuteReader())
                {

                    while (dataReader2.Read())
                    {
                        //Console.Write($"title: {dataReader[0]}\n");
                        Console.Write($"copies  of Movies includes: {dataReader2[0] }\n");
                    }
                }

            }

                 // Close connection
                conn.Close();
                Console.ReadLine();
            }
        }
    }

