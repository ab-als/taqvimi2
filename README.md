# taqvimi2

import 'package:flutter/material.dart';
import 'package:table_calendar/table_calendar.dart';
import 'package:intl/intl.dart'; // Datumsformatierung
import 'package:intl/date_symbol_data_local.dart';


void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await initializeDateFormatting('de_DE', null); // <- Lokalisierung laden
  runApp(const MyApp());
}


class MyApp extends StatelessWidget {
  const MyApp({super.key});
  
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Kalender App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const CalendarPage(),
    );
  }
}

class CalendarPage extends StatefulWidget {
  const CalendarPage({super.key});
  
  @override
  State<CalendarPage> createState() => _CalendarPageState();
}

class _CalendarPageState extends State<CalendarPage> {
  int _selectedIndex = 0;
  
  // Die vier Seiten
   static final List<Widget> _pages = <Widget>[
    OverviewPage(),    // Übersicht als Widget verwenden
    Center(child: Text('Tag')),
    Center(child: Text('Woche')),
    CalendarView(),         // Diese Seite enthält den Kalender
  ];

 // Diese Methode wechselt die Seite
  void _onItemTapped(int index) {
    setState(() {
      _selectedIndex = index;
    });
  }
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar( // die obere Leiste// App Überschrift
        title: const Text('Kalender App'),
      ),
  body: _pages[_selectedIndex], // Hauptinhalt //Zeigt je nach Auswahl die richtige Seite
      bottomNavigationBar: BottomNavigationBar( // Navigationselemente
        type: BottomNavigationBarType.fixed,
        items: const [
          BottomNavigationBarItem(
            icon: Icon(Icons.dashboard),
            label: 'Übersicht',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.today),
            label: 'Tag',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.view_week),
            label: 'Woche',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.calendar_month),
            label: 'Monat',
          ),
        ],
        currentIndex: _selectedIndex,
        onTap: _onItemTapped,
      ),
    );
  }
}

// CalendarViews
class CalendarView extends StatefulWidget {
  const CalendarView({Key? key}) : super(key: key);

  @override
  _CalendarViewState createState() => _CalendarViewState();
}

class _CalendarViewState extends State<CalendarView> {
  DateTime _focusedDay = DateTime.now();
  DateTime? _selectedDay;

  @override
  Widget build(BuildContext context) {
    return TableCalendar(
      firstDay: DateTime.utc(2020, 1, 1),
      lastDay: DateTime.utc(2030, 12, 31),
      focusedDay: _focusedDay,
      selectedDayPredicate: (day) {
        return isSameDay(_selectedDay, day);
      },
      onDaySelected: (selectedDay, focusedDay) {
        setState(() {
          _selectedDay = selectedDay;
          _focusedDay = focusedDay;
        });
      },
    );
  }
}


// Übersicht-Seite als eigenes Widget

// Datumsformatierung

class OverviewPage extends StatelessWidget {
  const OverviewPage({super.key});

  @override
  Widget build(BuildContext context) {
    // Textstil für die Überschrift falls zu ändern ist
      final TextStyle headingStyle = TextStyle(
      fontSize: 20,           // überschrift
      fontWeight: FontWeight.bold,
      color: Colors.black,     // Farbe für die Überschrift
    );

      // Größe der Rechtecke definieren
      //const double rectWidth = 150;
      //const double rectHeight = 130;

    // Aktuelles Datum
    final now = DateTime.now();
    final formattedDate = DateFormat ("d MMMM yyyy, EEEE", "de_DE").format(now);


      // Box Abstand
      //const double boxSpacing = 25; // Abstand zwischen den Boxen und Rand

      // Icon
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        children: [
          // Erste Zeile: Menü-Icon + Überschrift
          Row(
            children: [
                IconButton(
            icon: const Icon(Icons.menu),
            onPressed: (){
              //Menü später öffnen
              ScaffoldMessenger.of(context).showSnackBar(
                const SnackBar(content: Text("Menü geöffnet (nochleer)"))
              );
            },
          ),
             const SizedBox(width: 8), //Abstand zwischen Icon und Text
          Text(
             'Übersicht',
            style: headingStyle,
          ),
        ],
      ),

      const SizedBox(height: 12), //Abstand zwischen Überschrift und Balken

      //Balken mit Kalender-Icon und Datum
      Container(
        padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
        decoration: BoxDecoration(
          color: Colors.white,
          boxShadow: [
            //Schatten oben
          //  BoxShadow(
            //  color: Colors.black.withOpacity(0.05),
             // offset: const Offset(0, -2),
             // blurRadius: 4,
           // ),
            //Schatten unten
           // BoxShadow(
            //  color: Colors.black.withOpacity(0.15),
             // offset: const Offset(0, 4),
             // blurRadius: 6,
           // ),
          ],
          borderRadius: BorderRadius.circular(8),
       ),
       child: Row(
        children: [
          const Icon(Icons.calendar_today, color: Colors.blue),
          const SizedBox(width:8),
          Text(
            formattedDate,
            style: const TextStyle(fontSize: 16, color: Colors.black87),
          ),
        ],
       ),
      ),

      // Abstand der Quadrate zur Balken Datum
      const SizedBox(height: 24),

    // Zwei Quadrate
          Row(
            children: [
              Expanded(
                child: Container(
                  height: 160, //Höhe
                  padding: const EdgeInsets.all(12),
                  decoration: BoxDecoration(
                    color: Color.lerp(
                      Color.lerp(Colors.lightGreen, Colors.blue, 0.5), //Farbe
                      Colors.white, //Weiß Überlagerung
                      0.7,

                    ), borderRadius: BorderRadius.circular(12),
    ),
                   child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                   children:[
                     // Sonnen Symbol + Wetter
                     Row(
                     children: [
                       Icon(
                     Icons.wb_sunny,
                       color: Color.lerp(Colors.lightGreen, Colors.blue, 0.1),
                        size: 18,
                   ),
                    const SizedBox(width: 8),
                    const Text(
                      'Wetter',
                      style: TextStyle(
                        fontSize: 15,
                        fontWeight: FontWeight.bold,
                        color: Colors.black87,
                  ),
                ),
                    ],
              ),

    const SizedBox(height: 8),

    // Temperatur
Text(
    '26°C', // später dynamisch
    style: const TextStyle(
    fontSize: 24,
    fontWeight: FontWeight.bold,
    color: Colors.black,
    ),
    ),


    const SizedBox(height: 8),

    // Wetter-Icon zentriert (hier Sonne)

 Icon(
    Icons.wb_sunny,
    size: 32,
    color: Colors.orange,
    ),

    const SizedBox(height: 8),

    // Stadtname zentriert
 Text(
    'Dortmund',
    style: TextStyle(
    fontSize: 14,
    color: Colors.black54,
    ),
    ),


              ],
              ),
    ),
    ),


              const SizedBox(width: 16),
              // fester Abstand zwischen den Rechtecken
              Expanded(
                child: Container(
                  height: 160,
                  decoration: BoxDecoration(
                    color: Color.lerp(
                      Color.lerp(Colors.lightGreen, Colors.blue, 0.5), //Farbe
                      Colors.white,
                      0.7,
                    ),                    borderRadius: BorderRadius.circular(12),
                  ),
                ),
              ),
            ],
           ),
          // Später weitere Widgets einfügen
        ],
      ),
    );
  }
}
