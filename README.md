
import 'package:flutter/material.dart';

void main() {
  runApp(const WaslApp());
}

class WaslApp extends StatelessWidget {
  const WaslApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'وصل | Wasl',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorSchemeSeed: Colors.teal,
        useMaterial3: true,
        fontFamily: 'Roboto',
      ),
      home: const WaslHome(),
    );
  }
}

class WaslHome extends StatefulWidget {
  const WaslHome({super.key});

  @override
  State<WaslHome> createState() => _WaslHomeState();
}

class _WaslHomeState extends State<WaslHome> {
  int selectedTab = 0;

  final List<String> names = [
    'احمد',
    'فاطمه',
    'گروه دوستان',
  ];

  final List<String> previews = [
    'سلام، خوبی؟',
    'پیام صوتی',
    'به گروه خوش آمدید',
  ];

  void openChat(String name) {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => ChatPage(name: name),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final pages = [
      buildChats(),
      buildSimplePage(
        Icons.call,
        'تماس‌ها',
        'تماس‌های شما اینجا نمایش داده می‌شوند.',
      ),
      buildSimplePage(
        Icons.people,
        'مخاطبان',
        'در نسخه‌های بعدی مخاطبان را اضافه می‌کنیم.',
      ),
      buildSimplePage(
        Icons.settings,
        'تنظیمات',
        'تنظیمات پیام‌رسان وصل',
      ),
    ];

    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'وصل | Wasl',
          style: TextStyle(fontWeight: FontWeight.bold),
        ),
        centerTitle: true,
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
      ),
      body: pages[selectedTab],
      floatingActionButton: selectedTab == 0
          ? FloatingActionButton(
              backgroundColor: Colors.teal,
              foregroundColor: Colors.white,
              onPressed: () => openChat('گفتگوی جدید'),
              child: const Icon(Icons.chat),
            )
          : null,
      bottomNavigationBar: NavigationBar(
        selectedIndex: selectedTab,
        onDestinationSelected: (index) {
          setState(() {
            selectedTab = index;
          });
        },
        destinations: const [
          NavigationDestination(
            icon: Icon(Icons.chat_bubble_outline),
            selectedIcon: Icon(Icons.chat_bubble),
            label: 'گفتگوها',
          ),
          NavigationDestination(
            icon: Icon(Icons.call_outlined),
            label: 'تماس‌ها',
          ),
          NavigationDestination(
            icon: Icon(Icons.people_outline),
            label: 'مخاطبان',
          ),
          NavigationDestination(
            icon: Icon(Icons.settings_outlined),
            label: 'تنظیمات',
          ),
        ],
      ),
    );
  }

  Widget buildChats() {
    return ListView(
      padding: const EdgeInsets.all(12),
      children: [
        const Padding(
          padding: EdgeInsets.all(12),
          child: Text(
            'گفتگوهای شما',
            textAlign: TextAlign.right,
            style: TextStyle(
              fontSize: 22,
              fontWeight: FontWeight.bold,
            ),
          ),
        ),
        for (int i = 0; i < names.length; i++)
          Card(
            child: ListTile(
              onTap: () => openChat(names[i]),
              leading: CircleAvatar(
                backgroundColor: Colors.teal.shade50,
                child: Icon(
                  i == 2 ? Icons.groups : Icons.person,
                  color: Colors.teal,
                ),
              ),
              title: Text(
                names[i],
                textAlign: TextAlign.right,
              ),
              subtitle: Text(
                previews[i],
                textAlign: TextAlign.right,
              ),
              trailing: const Icon(Icons.chevron_left),
            ),
          ),
        const SizedBox(height: 20),
        const Text(
          'نسخه آزمایشی وصل',
          textAlign: TextAlign.center,
          style: TextStyle(color: Colors.grey),
        ),
      ],
    );
  }

  Widget buildSimplePage(
    IconData icon,
    String title,
    String message,
  ) {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(24),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(icon, size: 70, color: Colors.teal),
            const SizedBox(height: 16),
            Text(
              title,
              style: const TextStyle(
                fontSize: 24,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 12),
            Text(
              message,
              textAlign: TextAlign.center,
              style: const TextStyle(fontSize: 16),
            ),
          ],
        ),
      ),
    );
  }
}

class ChatPage extends StatefulWidget {
  final String name;

  const ChatPage({super.key, required this.name});

  @override
  State<ChatPage> createState() => _ChatPageState();
}

class _ChatPageState extends State<ChatPage> {
  final TextEditingController controller = TextEditingController();
  final List<String> messages = [
    'به پیام‌رسان وصل خوش آمدی!',
  ];

  void sendMessage() {
    final text = controller.text.trim();

    if (text.isEmpty) return;

    setState(() {
      messages.add(text);
      controller.clear();
    });
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.name),
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
        actions: [
          IconButton(
            onPressed: () {
              ScaffoldMessenger.of(context).showSnackBar(
                const SnackBar(
                  content: Text(
                    'تماس در نسخه‌های بعدی اضافه می‌شود.',
                  ),
                ),
              );
            },
            icon: const Icon(Icons.call),
          ),
        ],
      ),
      body: Column(
        children: [
          Expanded(
            child: ListView.builder(
              padding: const EdgeInsets.all(12),
              itemCount: messages.length,
              itemBuilder: (context, index) {
                return Align(
                  alignment: Alignment.centerRight,
                  child: Container(
                    margin: const EdgeInsets.symmetric(vertical: 5),
                    padding: const EdgeInsets.all(12),
                    constraints: const BoxConstraints(
                      maxWidth: 300,
                    ),
                    decoration: BoxDecoration(
                      color: Colors.teal.shade50,
                      borderRadius: BorderRadius.circular(14),
                    ),
                    child: Text(
                      messages[index],
                      textAlign: TextAlign.right,
                      style: const TextStyle(fontSize: 16),
                    ),
                  ),
                );
              },
            ),
          ),
          SafeArea(
            child: Padding(
              padding: const EdgeInsets.all(8),
              child: Row(
                children: [
                  IconButton(
                    onPressed: () {
                      ScaffoldMessenger.of(context).showSnackBar(
                        const SnackBar(
                          content: Text(
                            'ارسال عکس در نسخه بعدی اضافه می‌شود.',
                          ),
                        ),
                      );
                    },
                    icon: const Icon(
                      Icons.photo,
                      color: Colors.teal,
                    ),
                  ),
                  Expanded(
                    child: TextField(
                      controller: controller,
                      textAlign: TextAlign.right,
                      decoration: InputDecoration(
                        hintText: 'پیامت را بنویس...',
                        border: OutlineInputBorder(
                          borderRadius: BorderRadius.circular(24),
                        ),
                        contentPadding:
                            const EdgeInsets.symmetric(
                          horizontal: 16,
                          vertical: 10,
                        ),
                      ),
                      onSubmitted: (_) => sendMessage(),
                    ),
                  ),
                  IconButton(
                    onPressed: sendMessage,
                    icon: const Icon(
                      Icons.send,
                      color: Colors.teal,
                    ),
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}
