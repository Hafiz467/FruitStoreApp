# 🍎🍌🍒 FruitStoreApp

SwiftUI-based Fruit Store App - Select, view details, and purchase your favorite fruits. Features a dynamic UI, profile management, and a seamless add-to-cart functionality with multiple payment methods.

## Project Overview
This repository contains the code for a SwiftUI-based Fruit Store app. The app allows users to select and view details about various fruits, update their profile, and add items to a cart with different payment methods.

## Features

### Home Screen
- Fruit selection with quantity and pricing details
- Search functionality
- Dynamic UI with gradient backgrounds and shadows

### Detail View
- Detailed view of selected fruits with benefits

### Profile View
- Update profile picture and name

### Cart View
- Add to cart functionality
- Select payment method from various card options

## Code

```swift
import SwiftUI

struct ContenntView: View {
    @State private var profileImage: Image? = Image(systemName: "person.circle")
    var body: some View {
        TabView {
            HomeView(profileImage: $profileImage)
                .tabItem {
                    Image(systemName: "house.fill")
                    Text("Home")
                }
            FruitListView()
                .tabItem {
                    Image(systemName: "list.bullet")
                    Text("Detail")
                }
            ProfileView(profileImage: $profileImage)
                .tabItem {
                    Image(systemName: "person.fill")
                    Text("Profile")
                }
        }
    }
}

struct HomeView: View {
    @State private var selectedFruitIndex = 0
    @State private var fruitCounts = [0, 0, 0]
    @State private var showDetailView = false
    @State private var selectedFruit: Fruit?
    @State private var searchText = ""
    @Binding var profileImage: Image?
    @State private var navigateToProfile = false
    @State private var showAddToCart = false
    @State private var navigateToCart = false

    let fruits = [
        Fruit(name: "Apple", price: 1.0, imageName: "aaple", benefits: "Apples are nutritious, may be good for weight loss and heart health."),
        Fruit(name: "Banana", price: 0.5, imageName: "bbnana", benefits: "Bananas are rich in potassium, help in digestion, and provide instant energy."),
        Fruit(name: "Cherry", price: 2.0, imageName: "cchery", benefits: "Cherries are packed with antioxidants, anti-inflammatory compounds, and vitamins.")
    ]
    
    var filteredFruits: [Fruit] {
        if searchText.isEmpty {
            return fruits
        } else {
            return fruits.filter { $0.name.lowercased().contains(searchText.lowercased()) }
        }
    }
    
    var totalQuantity: Int {
        return fruitCounts.reduce(0, +)
    }
    
    var totalPrice: Double {
        return zip(fruitCounts, fruits).map { Double($0) * $1.price }.reduce(0, +)
    }
    
    var body: some View {
        NavigationView {
            ScrollView {
                VStack(spacing: 20) {
                    SearchBar(text: $searchText)
                        .cornerRadius(10)
                        .background(Color.mint)
                        .padding(.horizontal)
                    
                    VStack(spacing: 10) {
                        Text("Select Fruit")
                            .font(.headline)
                            .foregroundColor(.white)
                        
                        Picker("Select Fruit", selection: $selectedFruitIndex) {
                            ForEach(0..<filteredFruits.count, id: \.self) { index in
                                Text(self.filteredFruits[index].name).tag(index)
                            }
                        }
                        .pickerStyle(SegmentedPickerStyle())
                        .padding()
                        .background(LinearGradient(gradient: Gradient(colors: [Color.green.opacity(0.8), Color.blue.opacity(0.8)]), startPoint: .topLeading, endPoint: .bottomTrailing))
                        .cornerRadius(15)
                        .padding(.horizontal)
                        .shadow(color: .black.opacity(0.2), radius: 5, x: 0, y: 5)
                        
                        if !filteredFruits.isEmpty {
                            HStack {
                                Image(filteredFruits[selectedFruitIndex].imageName)
                                    .resizable()
                                    .scaledToFill()
                                    .frame(width: 100, height: 150)
                                    .clipShape(Circle())
                                    .shadow(radius: 10)
                                
                                VStack(alignment: .leading, spacing: 10) {
                                    Text("Quantity: \(fruitCounts[selectedFruitIndex])")
                                        .font(.title)
                                        .foregroundColor(.white)
                                    
                                    Text("Price: $\(Double(fruitCounts[selectedFruitIndex]) * filteredFruits[selectedFruitIndex].price, specifier: "%.2f")")
                                        .font(.title)
                                        .foregroundColor(.white)
                                }
                            }
                            .padding()
                        }
                    }
                    .padding()
                    .background(BlurView(style: .systemMaterialDark))
                    .cornerRadius(20)
                    .padding(.horizontal)
                    .shadow(color: .black.opacity(0.2), radius: 5, x: 0, y: 5)
                    
                    Stepper("Add \(filteredFruits.isEmpty ? "" : filteredFruits[selectedFruitIndex].name)", value: $fruitCounts[selectedFruitIndex], in: 0...100, onEditingChanged: { editing in
                        if fruitCounts[selectedFruitIndex] > 0 {
                            showAddToCart = true
                        }
                    })
                    .padding()
                    .background(LinearGradient(gradient: Gradient(colors: [Color.orange.opacity(0.8), Color.red.opacity(0.8)]), startPoint: .topLeading, endPoint: .bottomTrailing))
                    .cornerRadius(15)
                    .padding(.horizontal)
                    .shadow(color: .black.opacity(0.2), radius: 5, x: 0, y: 5)
                    
                    VStack(spacing: 10) {
                        Text("Total Quantity: \(totalQuantity)")
                            .font(.title)
                            .foregroundColor(.white)
                        
                        Text("Total Price: $\(totalPrice, specifier: "%.2f")")
                            .font(.title)
                            .foregroundColor(.white)
                        
                        if showAddToCart {
                            NavigationLink(destination: CartView(), isActive: $navigateToCart) {
                                Button(action: {
                                    self.navigateToCart = true
                                }) {
                                    Text("Add to Cart")
                                        .foregroundColor(.white)
                                        .padding(.horizontal)
                                        .padding(.vertical, 8)
                                        .background(Color.green)
                                        .cornerRadius(10)
                                        .shadow(color: .green.opacity(0.7), radius: 3, x: 0, y: 3)
                                }
                            }
                        }
                    }
                    .padding()
                    .background(BlurView(style: .systemMaterialDark))
                    .cornerRadius(20)
                    .padding(.horizontal)
                    .shadow(color: .black.opacity(0.2), radius: 5, x: 0, y: 5)
                    
                    ForEach(filteredFruits.indices, id: \.self) { index in
                        Button(action: {
                            self.selectedFruit = self.filteredFruits[index]
                            self.showDetailView = true
                        }) {
                            HStack {
                                Image(filteredFruits[index].imageName)
                                    .resizable()
                                    .scaledToFill()
                                    .frame(width: 100, height: 150)
                                    .clipShape(Circle())
                                    .shadow(radius: 10)
                                
                                VStack(alignment: .leading) {
                                    Text("\(filteredFruits[index].name)")
                                        .font(.title)
                                    Text("Quantity: \(fruitCounts[index])")
                                        .font(.title2)
                                    Text("Price: $\(Double(fruitCounts[index]) * filteredFruits[index].price, specifier: "%.2f")")
                                        .font(.title2)
                                }
                            }
                            .padding(.vertical, 5)
                        }
                    }
                }
                .padding()
                .background(
                    LinearGradient(gradient: Gradient(colors: [Color.blue.opacity(0.5), Color.purple.opacity(0.5)]), startPoint: .top, endPoint: .bottom)
                        .edgesIgnoringSafeArea(.all)
                )
                .navigationTitle("Fruits Store")
                .frame(alignment: .center)
                .sheet(isPresented: $showDetailView) {
                    if let selectedFruit = selectedFruit {
                        DetailFruitView(fruit: selectedFruit)
                    }
                }
                .navigationBarItems(
                    leading: Button(action: {
        
                    }) {
                        Image(systemName: "list.bullet")
                    },
                    trailing: NavigationLink(destination: ProfileView(profileImage: $profileImage), isActive: $navigateToProfile) {
                        profileImage?
                            .resizable()
                            .frame(width: 30, height: 30)
                            .clipShape(Circle())
                            .onTapGesture {
                                self.navigateToProfile = true
                            }
                    }
                )
            }
        }
    }
}

struct Fruit {
    var name: String
    var price: Double
    var imageName: String
    var benefits: String
}

struct BlurView: UIViewRepresentable {
    var style: UIBlurEffect.Style
    
    func makeUIView(context: Context) -> UIVisualEffectView {
        return UIVisualEffectView(effect: UIBlurEffect(style: style))
    }
    
    func updateUIView(_ uiView: UIVisualEffectView, context: Context) {
        uiView.effect = UIBlurEffect(style: style)
    }
}

struct DetailFruitView: View {
    var fruit: Fruit
    
    var body: some View {
        VStack(spacing: 20) {
            Image(fruit.imageName)
                .resizable()
                .scaledToFill()
                .frame(width: 100, height: 150)
                .clipShape(Circle())
                .shadow(radius: 10)
                .padding()
            
            Text(fruit.name)
                .font(.largeTitle)
                .fontWeight(.bold)
            
            Text(fruit.benefits)
                .font(.body)
                .padding()
        }
        .edgesIgnoringSafeArea(.all)
        .background(
            LinearGradient(gradient: Gradient(colors: [Color.pink.opacity(0.5), Color.orange.opacity(0.5)]), startPoint: .top, endPoint: .bottom)
                .edgesIgnoringSafeArea(.all)
        )
    }
}

struct SearchBar: UIViewRepresentable {
    @Binding var text: String
    
    class Coordinator: NSObject, UISearchBarDelegate {
        @Binding var text: String
        
        init(text: Binding<String>) {
            _text = text
        }
        
        func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
            text = searchText
        }
    }
    
    func makeCoordinator() -> Coordinator {
        return Coordinator(text: $text)
    }
    
    func makeUIView(context: UIViewRepresentableContext<SearchBar>) -> UISearchBar {
        let searchBar = UISearchBar(frame: .zero)
        searchBar.delegate = context.coordinator
        return searchBar
    }
    
    func updateUIView(_ uiView: UISearchBar, context: UIViewRepresentableContext<SearchBar>) {
        uiView.text = text
    }
}

struct FruitListView: View {
    let fruits = [
        FruitDetail(name: "Apple", price: 1.0, imageName: "aaple", benefits: "Apples are nutritious, may be good for weight loss and heart health."),
        FruitDetail(name: "Banana", price: 0.5, imageName: "bbnana", benefits: "Bananas are rich in potassium, help in digestion, and provide instant energy."),
        FruitDetail(name: "Cherry", price: 2.0, imageName: "cchery", benefits: "Cherries are packed with antioxidants, anti-inflammatory compounds, and vitamins.")
    ]
    
    var body: some View {
        NavigationView {
            List(fruits) { fruit in
                NavigationLink(destination: FruitDetailView(fruit: fruit)) {
                    HStack {
                        Image(fruit.imageName)
                            .resizable()
                            .frame(width: 50, height: 50)
                            .clipShape(Circle())
                        VStack(alignment: .leading) {
                            Text(fruit.name)
                                .font(.headline)
                            Text(fruit.benefits)
                                .font(.subheadline)
                                .lineLimit(2)
                        }
                    }
                    .frame(height: 200)
                }
            }
            .navigationTitle("Fruits")
            
        }
    }
}

struct FruitDetailView: View {
    var fruit: FruitDetail
    
    var body: some View {
        VStack(spacing: 20) {
            Image(fruit.imageName)
                .resizable()
                .scaledToFill()
                .frame(width: 100, height: 150)
                .clipShape(Circle())
                .shadow(radius: 10)
                .padding()
            
            Text(fruit.name)
                .font(.largeTitle)
                .fontWeight(.bold)
            
            Text(fruit.benefits)
                .font(.body)
                .padding()
        }
        .edgesIgnoringSafeArea(.all)
        .background(
            LinearGradient(gradient: Gradient(colors: [Color.pink.opacity(0.5), Color.orange.opacity(0.5)]), startPoint: .top, endPoint: .bottom)
                .edgesIgnoringSafeArea(.all)
        )
    }
}

struct FruitDetail: Identifiable {
    var id = UUID()
    var name: String
    var price: Double
    var imageName: String
    var benefits: String
}

import SwiftUI

struct ProfileView: View {
    @State private var name: String = ""
    @State private var showingImagePicker = false
    @State private var inputImage: UIImage?
    @Binding var profileImage: Image?
    
    var body: some View {
        NavigationView {
            VStack(spacing: 20) {
                profileImage?
                    .resizable()
                    .scaledToFill()
                    .frame(width: 150, height: 150)
                    .clipShape(Circle())
                    .shadow(radius: 10)
                    .padding()
                    .onTapGesture {
                        self.showingImagePicker = true
                    }
                
                TextField("Enter your name", text: $name)
                    .textFieldStyle(RoundedBorderTextFieldStyle())
                    .padding()
                    .background(Color.white)
                    .cornerRadius(10)
                    .shadow(color: .gray.opacity(0.3), radius: 3, x: 0, y: 3)
                    .padding(.horizontal)
                
                Spacer()
            }
            .padding()
            .background(
                LinearGradient(gradient: Gradient(colors: [Color.blue.opacity(0.5), Color.purple.opacity(0.5)]), startPoint: .top, endPoint: .bottom)
                    .edgesIgnoringSafeArea(.all)
            )
            .navigationTitle("Profile")
            .sheet(isPresented: $showingImagePicker, onDismiss: loadImage) {
                ImagePicker(image: self.$inputImage)
            }
        }
    }
    
    func loadImage() {
        guard let inputImage = inputImage else { return }
        profileImage = Image(uiImage: inputImage)
    }
}

struct ImagePicker: UIViewControllerRepresentable {
    class Coordinator: NSObject, UINavigationControllerDelegate, UIImagePickerControllerDelegate {
        let parent: ImagePicker
        
        init(parent: ImagePicker) {
            self.parent = parent
        }
        
        func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
            if let uiImage = info[.originalImage] as? UIImage {
                parent.image = uiImage
            }
            parent.presentationMode.wrappedValue.dismiss()
        }
    }
    
    @Environment(\.presentationMode) var presentationMode
    @Binding var image: UIImage?
    
    func makeCoordinator() -> Coordinator {
        Coordinator(parent: self)
    }
    
    func makeUIViewController(context: UIViewControllerRepresentableContext<ImagePicker>) -> UIImagePickerController {
        let picker = UIImagePickerController()
        picker.delegate = context.coordinator
        return picker
    }
    
    func updateUIViewController(_ uiViewController: UIImagePickerController, context: UIViewControllerRepresentableContext<ImagePicker>) {}
}

struct CartView: View {
    @State private var selectedCard: String? = nil
    let cards = [
        "Mastercard",
        "PayPal",
        "UnionPay",
        "Visa",
        "PayPak",
    ]

    var body: some View {
        VStack {
            Text("Select Your Payment Method")
                .font(.largeTitle)
                .padding()
            Spacer()
            List(cards, id: \.self) { card in
                HStack {
                    Image(card)
                        .resizable()
                        .frame(width: 100, height: 60)
                        .clipShape(Rectangle())
                        .cornerRadius(10)
                        .shadow(radius: 5)

                    Spacer()

                    RadioButtonField(
                        id: card,
                        label: card,
                        isMarked: self.selectedCard == card,
                        callback: { selected in
                            self.selectedCard = selected
                        }
                    )
                }
                .padding(.vertical, 10)
            }

            if let selectedCard = selectedCard {
                VStack {
                    Text("Selected Card")
                        .font(.headline)
                        .padding()
                    
                    Image(selectedCard)
                        .resizable()
                        .frame(width: 150, height: 90)
                        .clipShape(Rectangle())
                        .cornerRadius(10)
                        .shadow(radius: 5)
                        .padding()
                }
            }

            Spacer()
        }
        .padding()
        .background(
            LinearGradient(gradient: Gradient(colors: [Color.white, Color.gray.opacity(0.2)]), startPoint: .top, endPoint: .bottom)
                .edgesIgnoringSafeArea(.all)
        )
    }
}

struct RadioButtonField: View {
    let id: String
    let label: String
    let isMarked: Bool
    let callback: (String) -> ()

    var body: some View {
        Button(action: {
            self.callback(self.id)
        }) {
            HStack {
                Image(systemName: self.isMarked ? "largecircle.fill.circle" : "circle")
                    .foregroundColor(self.isMarked ? .blue : .gray)
                Text(label)
                    .font(.body)
            }
        }
        .foregroundColor(.primary)
    }
}

struct ContenntView_Previews: PreviewProvider {
    static var previews: some View {
        ContenntView()
    }
}
