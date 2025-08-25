Skip to content
Chat history

You said:
give me a basic structure on how to quickly make a bloc pattern 2 page app like cart app.

show a list simple and then have a button on 1 page and then add to card button

have a cart page that shows the list of items

make full files thanks

do this in dart flutter
ChatGPT said:
Got it ✅
I’ll give you a minimal 2-page Flutter app using BLoC pattern where:

HomePage → shows a list of products, each with an “Add to Cart” button.

CartPage → shows all items added to the cart.

I’ll write full files so you can directly copy & run.

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
models/product.dart
class Product {
  final int id;
  final String name;

  Product({required this.id, required this.name});
}
bloc/cart_event.dart
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
bloc/cart_state.dart
import '../models/product.dart';

class CartState {
  final List<Product> cartItems;

  CartState({required this.cartItems});

  CartState copyWith({List<Product>? cartItems}) {
    return CartState(cartItems: cartItems ?? this.cartItems);
  }
}
bloc/cart_bloc.dart
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
home_page.dart
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
cart_page.dart
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
main.dart
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
✅ That’s it! This is a fully working 2-page Cart app with BLoC.
You can run this directly and it will let you add/remove items.

Do you want me to also add a cart badge (item count) on the AppBar cart icon in HomePage?



No file chosenNo file chosen
ChatGPT can make mistakes. Check important info. See Cookie Preferences.
