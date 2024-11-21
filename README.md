local Utils = loadstring(game:HttpGet("LINK_PARTE_1"))()
local Config = Utils.Config
local Utility = Utils.Utility

-- Interface Principal
local Library = {}
local Window = {}
Window.__index = Window

function Library.new(title)
    -- Remove GUI anterior se existir
    if game:GetService("CoreGui"):FindFirstChild(Config.Name) then
        game:GetService("CoreGui"):FindFirstChild(Config.Name):Destroy()
    end
    
    -- GUI Principal
    local GUI = Utility.Create("ScreenGui", {
        Name = Config.Name,
        Parent = game:GetService("CoreGui"),
        ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    })
    
    -- Container Principal
    local Main = Utility.Create("Frame", {
        Name = "Main",
        BackgroundColor3 = Config.Theme.Background,
        BorderSizePixel = 0,
        Position = UDim2.new(0.5, -300, 0.5, -175),
        Size = UDim2.new(0, 600, 0, 350),
        Parent = GUI,
        ZIndex = 2
    })
    
    -- Adicionar visual
    Utility.AddCorner(Main)
    Utility.AddShadow(Main)
    
    -- Barra Superior
    local TopBar = Utility.Create("Frame", {
        Name = "TopBar",
        BackgroundColor3 = Config.Theme.Primary,
        Size = UDim2.new(1, 0, 0, 35),
        Parent = Main,
        ZIndex = 3
    })
    
    Utility.AddCorner(TopBar)
    Utility.AddGradient(TopBar)
    
    -- Título
    local Title = Utility.Create("TextLabel", {
        Name = "Title",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 12, 0, 0),
        Size = UDim2.new(1, -70, 1, 0),
        Font = Config.Font,
        Text = title or Config.Name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize + 2,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = TopBar,
        ZIndex = 3
    })
    
    -- Botões de Controle
    local CloseButton = Utility.Create("ImageButton", {
        Name = "CloseButton",
        BackgroundTransparency = 1,
        Position = UDim2.new(1, -30, 0.5, -8),
        Size = UDim2.new(0, 16, 0, 16),
        Image = "rbxassetid://9127564477",
        ImageColor3 = Config.Theme.Text,
        Parent = TopBar,
        ZIndex = 3
    })
    
    local MinimizeButton = Utility.Create("ImageButton", {
        Name = "MinimizeButton",
        BackgroundTransparency = 1,
        Position = UDim2.new(1, -55, 0.5, -8),
        Size = UDim2.new(0, 16, 0, 16),
        Image = "rbxassetid://9127563210",
        ImageColor3 = Config.Theme.Text,
        Parent = TopBar,
        ZIndex = 3
    })
    
    -- Container das Tabs
    local TabContainer = Utility.Create("Frame", {
        Name = "TabContainer",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(0, 8, 0, 43),
        Size = UDim2.new(0, 130, 1, -51),
        Parent = Main,
        ZIndex = 2
    })
    
    Utility.AddCorner(TabContainer)
    
    local TabList = Utility.Create("ScrollingFrame", {
        Name = "TabList",
        Active = true,
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 0, 0, 0),
        Size = UDim2.new(1, 0, 1, 0),
        ScrollBarThickness = 0,
        Parent = TabContainer,
        ZIndex = 2
    })
    
    local TabListLayout = Utility.Create("UIListLayout", {
        Padding = UDim.new(0, 5),
        SortOrder = Enum.SortOrder.LayoutOrder,
        Parent = TabList
    })
    
    Utility.AddPadding(TabList, 8)
    
    -- Container do Conteúdo
    local ContentContainer = Utility.Create("Frame", {
        Name = "ContentContainer",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 146, 0, 43),
        Size = UDim2.new(1, -154, 1, -51),
        Parent = Main,
        ZIndex = 2
    })
    
    -- Sistema de Minimizar
    local minimized = false
    local originalSize = Main.Size
    
    MinimizeButton.MouseButton1Click:Connect(function()
        minimized = not minimized
        
        if minimized then
            Utility.Tween(Main, {Size = UDim2.new(0, 600, 0, 35)})
            Utility.Tween(TabContainer, {BackgroundTransparency = 1})
            for _, child in pairs(ContentContainer:GetChildren()) do
                child.Visible = false
            end
        else
            Utility.Tween(Main, {Size = originalSize})
            Utility.Tween(TabContainer, {BackgroundTransparency = 0})
            if Window.CurrentTab then
                Window.CurrentTab.Content.Visible = true
            end
        end
    end)
    
    -- Fechar GUI
    CloseButton.MouseButton1Click:Connect(function()
        Utility.Tween(GUI, {BackgroundTransparency = 1}, 0.2)
        wait(0.2)
        GUI:Destroy()
    end)
    
    -- Sistema de Arrasto
    local dragging, dragInput, dragStart, startPos
    
    local function updateDrag(input)
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    
    TopBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = Main.Position
        end
    end)
    
    UIS.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            updateDrag(input)
        end
    end)
    
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    -- Sistema de Tabs
    function Window:CreateTab(name)
        local Tab = {}
        
        -- Botão da Tab
        local TabButton = Utility.Create("TextButton", {
            Name = name.."Tab",
            BackgroundColor3 = Config.Theme.Primary,
            BackgroundTransparency = 0.9,
            Size = UDim2.new(1, 0, 0, 32),
            Font = Config.Font,
            Text = name,
            TextColor3 = Config.Theme.Text,
            TextSize = Config.TextSize,
            Parent = TabList,
            ZIndex = 2
        })
        
        Utility.AddCorner(TabButton)
        
        -- Indicador da Tab
        local TabIndicator = Utility.Create("Frame", {
            Name = "Indicator",
            BackgroundColor3 = Config.Theme.Primary,
            Position = UDim2.new(0, 4, 0.5, -4),
            Size = UDim2.new(0, 0, 0, 8),
            Parent = TabButton,
            ZIndex = 2
        })
        
        Utility.AddCorner(TabIndicator, 4)
        
        -- Conteúdo da Tab
        local TabContent = Utility.Create("ScrollingFrame", {
            Name = name.."Content",
            BackgroundTransparency = 1,
            ClipsDescendants = true,
            Size = UDim2.new(1, 0, 1, 0),
            CanvasSize = UDim2.new(0, 0, 0, 0),
            ScrollBarThickness = 3,
            ScrollBarImageColor3 = Config.Theme.Primary,
            Visible = false,
            Parent = ContentContainer,
            ZIndex = 2
        })
        
        local ContentLayout = Utility.Create("UIListLayout", {
            Padding = UDim.new(0, 6),
            SortOrder = Enum.SortOrder.LayoutOrder,
            Parent = TabContent
        })
        
        Utility.AddPadding(TabContent, 8)
        
        -- Atualizar CanvasSize automaticamente
        ContentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            TabContent.CanvasSize = UDim2.new(0, 0, 0, ContentLayout.AbsoluteContentSize.Y + 16)
        end)
        
        -- Lógica de seleção da Tab
        TabButton.MouseButton1Click:Connect(function()
            if Window.CurrentTab then
                -- Desativar tab atual
                Utility.Tween(Window.CurrentTab.Indicator, {Size = UDim2.new(0, 0, 0, 8)})
                Window.CurrentTab.Button.BackgroundTransparency = 0.9
                Window.CurrentTab.Content.Visible = false
            end
            
            -- Ativar nova tab
            Window.CurrentTab = {
                Button = TabButton,
                Indicator = TabIndicator,
                Content = TabContent
            }
            
            Utility.Tween(TabIndicator, {Size = UDim2.new(0, 4, 0, 8)})
            Utility.Tween(TabButton, {BackgroundTransparency = 0.7})
            if not minimized then
                TabContent.Visible = true
            end
        end)
        
        return Tab
    end
    
    -- Atalho para minimizar (Ctrl + M)
    Utility.KeyPressed(Enum.KeyCode.M, function()
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
            MinimizeButton.MouseButton1Click:Fire()
        end
    end)
    
    return Window
end

return Library
