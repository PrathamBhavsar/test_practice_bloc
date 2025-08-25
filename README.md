🛒 Flutter BLoC Cart App
A simple 2-page Flutter app demonstrating the BLoC pattern.
- Home Page: Shows a list of products with 'Add to Cart' buttons.
- Cart Page: Displays the list of items added to the cart, with the option to remove them.

This is a minimal example meant for beginners to quickly understand how to set up BLoC with navigation and state management.
🚀 Features
- Product list with 'Add to Cart' button
- Cart page showing selected items
- Remove items from the cart
- Simple BLoC pattern with Event + State + Bloc
📂 Project Structure
lib/
 ├─ main.dart
 ├─ home_page.dart
 ├─ cart_page.dart
 ├─ bloc/
 │   ├─ cart_bloc.dart
 │   ├─ cart_event.dart
 │   └─ cart_state.dart
 └─ models/
     └─ product.dart
🛠️ Setup & Run
1. Clone the repo or copy the files into a new Flutter project.
2. Add dependencies in pubspec.yaml:
   dependencies:
     flutter:
       sdk: flutter
     flutter_bloc: ^8.1.3
3. Get packages:
   flutter pub get
4. Run the app:
   flutter run
lib/models/product.dart
class Product {
  final int id;
  final String name;

  Product({required this.id, required this.name});
}
lib/bloc/cart_event.dart
import '../models/product.dart';

abstract class CartEvent {}

class AddToCart extends CartEvent {
  final Product product;
  AddToCart(this.product);
}

class RemoveFromCart extends CartEvent {
  final Product product;
  RemoveFromCart(this.product);
}
lib/bloc/cart_state.dart
import '../models/product.dart';

class CartState {
  final List<Product> cartItems;

  CartState({required this.cartItems});

  CartState copyWith({List<Product>? cartItems}) {
    return CartState(cartItems: cartItems ?? this.cartItems);
  }
}
lib/bloc/cart_bloc.dart
import 'package:flutter_bloc/flutter_bloc.dart';
import 'cart_event.dart';
import 'cart_state.dart';

class CartBloc extends Bloc<CartEvent, CartState> {
  CartBloc() : super(CartState(cartItems: [])) {
    on<AddToCart>((event, emit) {
      final updated = List.from(state.cartItems)..add(event.product);
      emit(state.copyWith(cartItems: updated));
    });

    on<RemoveFromCart>((event, emit) {
      final updated = List.from(state.cartItems)..remove(event.product);
      emit(state.copyWith(cartItems: updated));
    });
  }
}
lib/home_page.dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'bloc/cart_bloc.dart';
import 'bloc/cart_event.dart';
import 'models/product.dart';
import 'cart_page.dart';

class HomePage extends StatelessWidget {
  HomePage({super.key});

  final products = List.generate(
    5,
    (index) => Product(id: index, name: "Product ${index + 1}"),
  );

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Shop"),
        actions: [
          IconButton(
            icon: const Icon(Icons.shopping_cart),
            onPressed: () {
              Navigator.push(
                context,
                MaterialPageRoute(builder: (_) => const CartPage()),
              );
            },
          )
        ],
      ),
      body: ListView.builder(
        itemCount: products.length,
        itemBuilder: (_, index) {
          final product = products[index];
          return ListTile(
            title: Text(product.name),
            trailing: ElevatedButton(
              onPressed: () {
                context.read<CartBloc>().add(AddToCart(product));
              },
              child: const Text("Add to Cart"),
            ),
          );
        },
      ),
    );
  }
}
lib/cart_page.dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'bloc/cart_bloc.dart';
import 'bloc/cart_state.dart';
import 'bloc/cart_event.dart';
import 'models/product.dart';

class CartPage extends StatelessWidget {
  const CartPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Cart")),
      body: BlocBuilder<CartBloc, CartState>(
        builder: (context, state) {
          if (state.cartItems.isEmpty) {
            return const Center(child: Text("Cart is empty"));
          }
          return ListView.builder(
            itemCount: state.cartItems.length,
            itemBuilder: (_, index) {
              final Product product = state.cartItems[index];
              return ListTile(
                title: Text(product.name),
                trailing: IconButton(
                  icon: const Icon(Icons.remove_circle),
                  onPressed: () {
                    context.read<CartBloc>().add(RemoveFromCart(product));
                  },
                ),
              );
            },
          );
        },
      ),
    );
  }
}
lib/main.dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'bloc/cart_bloc.dart';
import 'home_page.dart';

void main() {
  runApp(
    BlocProvider(
      create: (_) => CartBloc(),
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Cart App',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: HomePage(),
    );
  }
}
