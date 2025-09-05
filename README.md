
import 'package:flutter/material.dart';
import 'package:flutter_pdfview/flutter_pdfview.dart';
import 'package:path_provider/path_provider.dart';
import 'dart:io';
import 'package:flutter/services.dart' show rootBundle;

void main() {
  runApp(BookApp());
}

class BookApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'আমার বই অ্যাপ',
      theme: ThemeData(primarySwatch: Colors.deepPurple),
      home: BookListPage(),
    );
  }
}

class BookListPage extends StatelessWidget {
  final List<Map<String, String>> books = [
    {"title": "কবিতা সংকলন", "file": "assets/book1.pdf"},
    {"title": "বিজ্ঞান বই", "file": "assets/book2.pdf"},
    {"title": "ইতিহাসের গল্প", "file": "assets/book3.pdf"},
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("আমার লাইব্রেরি")),
      body: ListView.builder(
        itemCount: books.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(books[index]["title"]!),
            trailing: Icon(Icons.arrow_forward),
            onTap: () async {
              String path = await _loadPdfFromAssets(books[index]["file"]!);
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => PdfViewPage(path: path),
                ),
              );
            },
          );
        },
      ),
    );
  }

  Future<String> _loadPdfFromAssets(String asset) async {
    final byteData = await rootBundle.load(asset);
    final file = File("${(await getTemporaryDirectory()).path}/${asset.split('/').last}");
    await file.writeAsBytes(byteData.buffer.asUint8List());
    return file.path;
  }
}

class PdfViewPage extends StatelessWidget {
  final String path;
  PdfViewPage({required this.path});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("বই পড়া")),
      body: PDFView(filePath: path),
    );
  }
}
