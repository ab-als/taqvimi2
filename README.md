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
      appBar: AppBar(
        title: const Text('Kalender App'),
      ),
  body: _pages[_selectedIndex], // Zeigt je nach Auswahl die richtige Seite
      bottomNavigationBar: BottomNavigationBar(
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

// Aktueller Datum Importieren 
// Datumsformatierung

class OverviewPage extends StatelessWidget {
  const OverviewPage({super.key});

  @override
  Widget build(BuildContext context) {
    // Textstil für die Überschrift falls zu ändern ist
      final TextStyle headingStyle = TextStyle(
      fontSize: 20,           // überschrift
      fontWeight: FontWeight.bold,
      color: Colors.black,     // Farbe 
    );

    // Aktuelles Datum 
    final now = DateTime.now();
    final formattedDate = DateFormat ("d MMMM yyyy, EEEE", "de_DE").format(now);

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
            BoxShadow(
              color: Colors.black.withOpacity(0.05),
              offset: const Offset(0, -2),
              blurRadius: 4,
            ),
            //Schatten unten 
            BoxShadow(
              color: Colors.black.withOpacity(0.15),
              offset: const Offset(0, 4),
              blurRadius: 6,
            ),
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
       // Später weitere Widgets einfügen
        ],
      ),
    );
  }
}
