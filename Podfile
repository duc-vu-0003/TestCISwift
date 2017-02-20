# Uncomment this line to define a global platform for your project
platform :ios, '9.0'
# Uncomment this line if you're using Swift
use_frameworks!

def shared_pods
    # Swift
    pod 'AlamofireObjectMapper'
    pod 'SwiftyUserDefaults'
    pod 'Kingfisher'
    pod 'IQKeyboardManagerSwift'
    pod 'MXSegmentedPager'

    #Designable
    pod 'IBAnimatable'

    #Menu
    pod 'CircleMenu','~> 2.0.1'
    pod 'BubbleTransition'

    # 3rd framework
    pod 'OAuthSwift'
end

target 'MyTestApplication' do
    shared_pods
end

target 'MyTestApplicationTests' do
    shared_pods
end

target 'MyTestApplicationUITests' do
    shared_pods
end

post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
            config.build_settings['SWIFT_VERSION'] = '3.0'
        end
    end
end
