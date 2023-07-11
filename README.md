# swiftui_custom_time_picker_uiview

```swift
//
//  CustomTimePicker.swift
//  MiracleNight
//
//  Created by paige shin on 2023/01/17.
//

import SwiftUI

private var hours: [String] = (0 ..< 24).map { String(format: "%02d", $0) }

private var minutes: [String] = ["00", "15", "30", "45"]

// #if DEBUG
// fileprivate var minutes: [String] = (0..<60).map{ String(format: "%02d", $0) }
// #else
// fileprivate var minutes: [String] = ["00", "15", "30", "45"]
// #endif

struct CustomTimePicker: UIViewRepresentable {
    @Binding var hour: Int
    @Binding var minute: Int
    let onSelect: (_ hour: Int, _ minute: Int) -> Void

    func makeUIView(context: UIViewRepresentableContext<CustomTimePicker>) -> UIPickerView {
        let picker = UIPickerView()
        picker.dataSource = context.coordinator
        picker.delegate = context.coordinator

        let hourIndex: Int = hours.firstIndex(where: { Int($0) == self.hour }) ?? hour
        picker.selectRow(hourIndex, inComponent: 0, animated: false)

        let minuteIndex: Int = minutes.firstIndex(where: { Int($0) == self.minute }) ?? minute
        picker.selectRow(minuteIndex, inComponent: 2, animated: false)
        return picker
    }

    func updateUIView(_ uiView: CustomTimePicker.UIViewType, context _: UIViewRepresentableContext<CustomTimePicker>) {
        let hourIndex: Int = hours.firstIndex(where: { Int($0) == self.hour }) ?? hour
        uiView.selectRow(hourIndex, inComponent: 0, animated: true)

        let minuteIndex: Int = minutes.firstIndex(where: { Int($0) == self.minute }) ?? minute
        uiView.selectRow(minuteIndex, inComponent: 2, animated: true)
    }

    func makeCoordinator() -> Coordinator {
        return CustomTimePicker.Coordinator(hour: $hour, minute: $minute, onSelect: onSelect)
    }

    class Coordinator: NSObject, UIPickerViewDelegate, UIPickerViewDataSource {
        @Binding private var hour: Int
        @Binding private var minute: Int
        private var onSelect: (_ hour: Int, _ minute: Int) -> Void

        init(hour: Binding<Int>, minute: Binding<Int>, onSelect: @escaping (_ hour: Int, _ minute: Int) -> Void) {
            _hour = hour
            _minute = minute
            self.onSelect = onSelect
        }

        func pickerView(_: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
            switch component {
            case 0: return hours.count
            case 1: return 1
            case 2: return minutes.count
            default: return 0
            }
        }

        func numberOfComponents(in _: UIPickerView) -> Int {
            return 3
        }

        func pickerView(_: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
            switch component {
            case 0:
                let position: Int = row % hours.count
                return hours[position]
            case 1: return ""
            case 2:
                let position: Int = row % minutes.count
                return minutes[position]
            default: return ""
            }
        }

        /// Item Design
        func pickerView(_ pickerView: UIPickerView, viewForRow row: Int, forComponent component: Int, reusing view: UIView?) -> UIView {
            pickerView.subviews[1].alpha = 0

            switch component {
            case 0, 2:
                let view: UIView = .init(frame: CGRect(x: 0, y: 0, width: 60, height: 60))
                view.backgroundColor = .clear

                let label = UILabel(frame: CGRect(x: 0, y: 0, width: view.bounds.width, height: view.bounds.height))

                label.textColor = UIColor(red: 119 / 255, green: 156 / 255, blue: 255 / 255, alpha: 1)
                label.textAlignment = .center
                label.font = .systemFont(ofSize: 42, weight: .regular)
                label.text = ":"

                view.addSubview(label)

                switch component {
                case 0:
                    let position: Int = row % hours.count
                    label.text = hours[position]
                case 2:
                    let position: Int = row % minutes.count
                    label.text = minutes[position]
                default: break
                }

                return view
            case 1:
                let view: UIView = .init(frame: CGRect(x: 0, y: 0, width: 20, height: 60))
                view.backgroundColor = .clear

                let label = UILabel(frame: CGRect(x: 0, y: 0, width: view.bounds.width, height: view.bounds.height))

                label.textColor = UIColor(red: 119 / 255, green: 156 / 255, blue: 255 / 255, alpha: 1)
                label.textAlignment = .center
                label.font = .systemFont(ofSize: 38, weight: .regular)
                label.text = ":"

                view.addSubview(label)
                return view
            default:
                return UIView()
            }
        }

        /// Container Width
        func pickerView(_: UIPickerView, widthForComponent component: Int) -> CGFloat {
            switch component {
            case 0, 2:
                return 60
            case 1:
                return 20
            default:
                return 0
            }
        }

        /// Item Height
        func pickerView(_: UIPickerView, rowHeightForComponent _: Int) -> CGFloat {
            return 60
        }

        func pickerView(_: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
            switch component {
            case 0:
                let position: Int = row
                hour = Int(hours[position])!
            case 2:
                let position: Int = row
                minute = Int(minutes[position])!
            default: break
            }
            onSelect(hour, minute)
        }
    }
}

#if DEBUG
    struct CustomTimePicker_Previews: PreviewProvider {
        static var previews: some View {
            CustomTimePicker(hour: .constant(0), minute: .constant(30), onSelect: { _, _ in })
        }
    }
#endif

```
