week-3(a)
import 'package:flutter/material.dart';

void main() {
  runApp(const ResponsiveUIDemo());
}

class ResponsiveUIDemo extends StatelessWidget {
  const ResponsiveUIDemo({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Responsive UI Demo',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        useMaterial3: true,
        primarySwatch: Colors.teal,
      ),
      home: const ResponsiveHomePage(),
    );
  }
}

class ResponsiveHomePage extends StatelessWidget {
  const ResponsiveHomePage({super.key});

  @override
  Widget build(BuildContext context) {
    final screenSize = MediaQuery.of(context).size;

    return Scaffold(
      appBar: AppBar(
        title: const Text('Responsive UI', style: TextStyle(color: Colors.white)),
        backgroundColor: Colors.teal,
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // Screen Size Header Banner
            Container(
              width: double.infinity,
              padding: const EdgeInsets.all(16),
              color: Colors.amber[100],
              child: Text(
                screenSize.width < 400
                    ? 'Small Screen'
                    : screenSize.width < 800
                        ? 'Medium Screen'
                        : 'Large Screen',
                style: const TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                textAlign: TextAlign.center,
              ),
            ),
            const SizedBox(height: 16),

            // Adaptive Layout Builder (Row vs Column)
            LayoutBuilder(
              builder: (context, constraints) {
                if (constraints.maxWidth < 600) {
                  return Column(
                    children: buildResponsiveWidgets(),
                  );
                } else {
                  return Row(
                    mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                    children: buildResponsiveWidgets()
                        .map((w) => Expanded(child: w))
                        .toList(),
                  );
                }
              },
            ),
            const SizedBox(height: 16),

            // Orientation Display Block
            OrientationBuilder(
              builder: (context, orientation) {
                return Center(
                  child: Text(
                    orientation == Orientation.portrait ? 'Portrait Mode' : 'Landscape Mode',
                    style: const TextStyle(fontSize: 16, fontWeight: FontWeight.w500),
                  ),
                );
              },
            ),
            const SizedBox(height: 16),

            // Grid Layout Section
            const Text(
              'Responsive GridView:',
              style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            GridView.count(
              shrinkWrap: true, // Crucial fix: Allows GridView to live inside SingleChildScrollView
              physics: const NeverScrollableScrollPhysics(), // Crucial fix: Delegates scrolling up to parent
              crossAxisCount: screenSize.width < 600 ? 2 : 4,
              crossAxisSpacing: 10,
              mainAxisSpacing: 10,
              children: List.generate(
                8,
                (index) => Container(
                  color: Colors.blue[100 * ((index % 8) + 1)],
                  child: Center(
                    child: Text(
                      'Item ${index + 1}',
                      style: const TextStyle(fontWeight: FontWeight.bold),
                    ),
                  ),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }

  List<Widget> buildResponsiveWidgets() {
    return [
      Container(
        margin: const EdgeInsets.all(8),
        padding: const EdgeInsets.all(16),
        color: Colors.red[100],
        width: double.infinity,
        child: const Column(
          children: [
            Icon(Icons.phone_android, size: 40, color: Colors.red),
            SizedBox(height: 8),
            Text('Mobile Friendly', style: TextStyle(fontWeight: FontWeight.w500)),
          ],
        ),
      ),
      Container(
        margin: const EdgeInsets.all(8),
        padding: const EdgeInsets.all(16),
        color: Colors.green[100],
        width: double.infinity,
        child: const Column(
          children: [
            Icon(Icons.tablet, size: 40, color: Colors.green),
            SizedBox(height: 8),
            Text('Tablet Ready', style: TextStyle(fontWeight: FontWeight.w500)),
          ],
        ),
      ),
    ];
  }
}




week-3(b)
import 'package:flutter/material.dart';

void main() => runApp(const MyResponsiveApp());

class MyResponsiveApp extends StatelessWidget {
  const MyResponsiveApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Responsive UI with MediaQuery',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        useMaterial3: true,
        primarySwatch: Colors.deepPurple,
      ),
      home: const ResponsiveHomePage(),
    );
  }
}

class ResponsiveHomePage extends StatelessWidget {
  const ResponsiveHomePage({super.key});

  @override
  Widget build(BuildContext context) {
    final screenSize = MediaQuery.of(context).size;
    final double screenWidth = screenSize.width;

    // Breakpoints definitions
    final bool isMobile = screenWidth < 600;
    final bool isTablet = screenWidth >= 600 && screenWidth < 1024;
    final bool isDesktop = screenWidth >= 1024;

    // Responsive Padding configurations
    final EdgeInsets contentPadding = EdgeInsets.symmetric(
      horizontal: isMobile ? 16 : isTablet ? 32 : 64,
      vertical: 20,
    );

    // Responsive Typography configurations
    final double titleFontSize = isMobile ? 24 : isTablet ? 28 : 32;
    final double contentFontSize = isMobile ? 16 : isTablet ? 18 : 20;

    // Responsive structural layout generator
    Widget buildResponsiveLayout() {
      if (isMobile) {
        return Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: _buildWidgets(titleFontSize, contentFontSize),
        );
      } else {
        return Row(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: _buildWidgets(titleFontSize, contentFontSize),
              ),
            ),
            if (isDesktop) ...[
              const SizedBox(width: 32), // Layout gap for desktop view
              Expanded(
                child: Container(
                  height: 350,
                  color: Colors.grey[200],
                  child: const Center(
                    child: Text(
                      'Extra Panel for Desktop',
                      style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                    ),
                  ),
                ),
              ),
            ],
          ],
        );
      }
    }

    return Scaffold(
      appBar: AppBar(
        title: const Text("Responsive Layout", style: TextStyle(color: Colors.white)),
        backgroundColor: Colors.deepPurple,
      ),
      body: SingleChildScrollView(
        child: Padding(
          padding: contentPadding,
          child: buildResponsiveLayout(),
        ),
      ),
    );
  }

  // Master method containing central content widgets
  List<Widget> _buildWidgets(double titleFontSize, double contentFontSize) {
    // Standard static purple spectrum colors for error-free rendering
    final List<Color?> purpleShades = [
      Colors.deepPurple[50],
      Colors.deepPurple[100],
      Colors.deepPurple[200],
      Colors.deepPurple[300],
      Colors.deepPurple[400],
      Colors.deepPurple[500],
    ];

    return [
      Text(
        'Welcome to My App',
        style: TextStyle(fontSize: titleFontSize, fontWeight: FontWeight.bold),
      ),
      const SizedBox(height: 20),
      Text(
        'This layout adapts based on screen width using MediaQuery and breakpoints.',
        style: TextStyle(fontSize: contentFontSize),
      ),
      const SizedBox(height: 20),
      Container(
        height: 150,
        width: double.infinity,
        color: Colors.teal[100],
        child: Center(
          child: Text(
            'Responsive Container',
            style: TextStyle(fontSize: contentFontSize, fontWeight: FontWeight.w500),
          ),
        ),
      ),
      const SizedBox(height: 20),
      Wrap(
        spacing: 10,
        runSpacing: 10,
        children: List.generate(
          6,
          (index) => Container(
            width: 100,
            height: 100,
            color: purpleShades[index % purpleShades.length],
            child: Center(
              child: Text(
                'Box ${index + 1}',
                style: const TextStyle(fontWeight: FontWeight.bold),
              ),
            ),
          ),
        ),
      ),
    ];
  }
}
